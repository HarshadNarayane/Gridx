The [gridx/modules/Filter](http://oria.github.io/gridx/apidoc/index.html#1.2/gridx/modules/Filter) module provides some APIs to filter gridx. Note this is a non-UI module, which can be a common base for various filter UIs, such as FilterBar or QuickFilter. This article is only about this non-UI module, for more details on [FilterBar](http://oria.github.io/gridx/apidoc/index.html#1.2/gridx/modules/filter/FilterBar) or [QuickFilter](http://oria.github.io/gridx/apidoc/index.html#1.2/gridx/modules/filter/QuickFilter), please refer to other tutorials or their API docs.

This Filter module is not a core/default module, so it has to be explicitly required and declared when creating gridx:

<pre>
require([
     ......
     <b>"gridx/modules/Filter",</b>
     ......
], function(..., <b>Filter,</b> ...){
     ......
     var grid = new Grid({
          ......
          modules: [
               ......
               <b>Filter,</b>
               ......
          ]
     });
     ......
});
</pre>

A gridx instance with this module will have a setFilter method taking any "predicate" function as its filter condition:

<pre>
grid.filter.setFilter(function(rowCache, rowId){
     // return true to keep this row, false to filter this row.
});
</pre>

After this call, grid will update itself automatically. But of course, the filter is happening at client side now. Gridx will scan every row in current store, using the predicate function to filter them. This will be very slow if we are using server side store.

### Server-side Filter
For server side store, filter logic should usually be done at server. So all the gridx need to do is to pass the filter conditions to server.

To enable server side filtering, we need to configure our grid as follows:

<pre>
     var grid = new Grid({
          ......
          <b>filterServerMode: true,
          filterSetupQuery: function(expr){
               // return the filter query that your server can understand.
          },</b>
          modules: [
               ......
               Filter,
               ......
          ]
     });
</pre>

Here, filterServerMode is the "[serverMode](http://oria.github.io/gridx/apidoc/index.html#1.2/gridx/modules/Filter#serverMode)" parameter of Filter module, and filterSetupQuery, the "[setupQuery](http://oria.github.io/gridx/apidoc/index.html#1.2/gridx/modules/Filter#setupQuery)" parameter of Filter module. By setting serverMode to true, we are telling Filter module to bypass the client filtering logic, and directly send filter conditions (the "expr" argument) to store's query parameter. But what does this query look like? Gridx has provided a default format, but your server might not understand. So the setupQuery function is to define your own filter query for your own server.

The returned filter query in this function will be passed to store like this:

<pre>
store.query(filterQuery, {
     start: ...,
     count: ...,
     ...
});
</pre>

And in current server side store implementation, such as dojo/store/JsonRest, this filterQuery will be eventually appended to the URL as a query string.

Now the question is, what is this "expr"?

### Filter Expressions
In gridx a filter condition is an arbitrary predicate function, so that any logic can be applied here. But a function can not be passed to server side, we have to serialize it.

In [gridx/modules/Filter.js](http://oria.github.io/gridx/apidoc/index.html#1.2/gridx/modules/Filter.__FilterExpressionTools), a bunch of helper functions are provided. They are called filter expression functions. They can be used to construct very complicated filter conditions for gridx, while still easy to serialize to JSON. Here's an example on how to use these functions:

<pre>
                    var expr = F.and(
                              F.and(
                                   F.startWith(F.column('colA', 'string'), F.value('123abc', 'string')),
                                   F.greater(F.column('colB', 'number'), F.value(456, 'number'))
                              ),
                              F.or(
                                   F.lessEqual(F.column('colC', 'number'), F.value(89, 'number')),
                                   F.not(
                                        F.endWith(F.column('colD', 'string'), F.value('xyz', 'string'))
                                   )
                              )
                         );
</pre>

Here the result expr is still a predicate function, so it can be directly passed to grid.filter.setFilter(). But it is more than that, because it can be serialized to JSON:

<pre>
var exprJsonObj = expr.expr;
</pre>

This object has a recursive structure, representing the nested function call structure when we created the expression:

<pre>
{
     op: 'and',
     data: [
          {
               op: 'and',
               data: [
                    {
                         op: 'startWith',
                         data: [
                              {
                                   op: 'string',
                                   data: 'colA',
                                   isCol: true
                              },
                              {
                                   op: 'string',
                                   data: '123abc'
                              }
                         ]
                    },
                    {
                         op: 'greater',
                         data: [
                              {
                                   op: 'number',
                                   data: 'colB',
                                   isCol: true
                              },
                              {
                                   op: 'number',
                                   data: 456
                              }
                         ]
                    }
               ]
          },
          {
               op: 'or',
               data: [
                    {
                         op: 'lessEqual',
                         data: [
                              {
                                   op: 'number',
                                   data: 'colC',
                                   isCol: true
                              },
                              {
                                   op: 'number',
                                   data: 89
                              }
                         ]
                    },
                    {
                         op: 'not',
                         data: [
                              {
                                   op: 'endWith',
                                   data: [
                                        {
                                             op: 'string',
                                             data: 'colD',
                                             isCol: true
                                        },
                                        {
                                             op: 'string',
                                             data: 'xyz'
                                        }
                                   ]
                              }
                         ]
                    }
               ]
          }
     ]
}
</pre>

This structure is suitable for arbitrary condition, but seems too complicated if the filter condition pattern is limited. For example, filter conditions generated by the [FilterBar](http://oria.github.io/gridx/apidoc/index.html#1.2/gridx/modules/filter/FilterBar) module never include nested operations, so its pattern can be simplified for server side. That's why the setupQuery function should always be customized.

Currently the filter expressions provided in Filter.js can support the following data types:

<pre>
string, number, date, time, boolean
</pre>

"string" is the default type, if no type is provided.
And support the following operations:

<pre>
and, or, not, equal, greater, less, greaterEqual, lessEqual, match, contain, startWith, endWith
</pre>

More details on these functions can be found in their [API docs](http://oria.github.io/gridx/apidoc/index.html#1.2/gridx/modules/Filter.__FilterExpressionTools).

