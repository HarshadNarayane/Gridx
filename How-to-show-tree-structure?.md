The [Tree](http://oria.github.io/gridx/apidoc/index.html#1.2/gridx/modules/Tree) module is needed to show tree structure in Gridx:

<pre>
var grid = new Grid({
     ......
     modules: [
          "gridx/modules/Tree"
     ],
     ......
});
</pre>

But that's not enough, let's see what else should/could be done.

### Prepare the Store
If your store does not support tree data, then neither does Gridx. Every tree store for Gridx should implement the following two methods:

<pre>
store.hasChildren = function(id, item){
               // summary:
               //          Check whether a row has child rows. This function should not throw any error.
               // id: String|Number
               //          The row ID
               // item: Object
               //          The store item
               // returns:
               //          True if the given row has children, false otherwise.
};

store.getChildren = function(item){
               // summary:
               //          Get an array of the child items of the given row item.
               // item: Object
               //          The store item
               // returns:
               //          An array of the child items of the given row item.
};
</pre>

The [hasChildren](http://oria.github.io/gridx/apidoc/index.html#1.2/gridx/modules/Tree.__TreeStoreMixin#hasChildren) function should always work synchronously. Gridx need this function to tell whether a row has child rows. If you are using dojo/store/Memory and all the children data are stored are directly put a "children" field of every item, then this function could be written as:

<pre>
store.hasChildren = function(id, item){
     // we should be very careful here, because hasChildren should not throw.
     return item && item.children && item.children.length; 
};
</pre>

The [getChildren](http://oria.github.io/gridx/apidoc/index.html#1.2/gridx/modules/Tree.__TreeStoreMixin#getChildren) function should return an array of child items. But if your child data are on server, you might need to operate asynchronously here by returning a Deferred object:

<pre>
store.getChildren = function(item){
     var d = new Deferred();
     // Suppose the child rows won't be ready until 2 seconds later.
     setTimeout(function(){
          d.callback([....]);
     }, 2000);
     return d;
};
</pre>

If you are just ordinary user, you can stop here because your tree grid is already working. If you need more advanced configurations, please move on.

### Decide where to put the expandoes
With the store ready, you can already run your tree grid. If you don't provide other parameters, all the expandoes will be in the first column:

![first column expandoes](http://oria.github.io/gridx/tutor/image/gridx-13.png)

What if you want the expansoes to appear somewhere else? Actually you can explicitly declare this in your column structure:

<pre>
structure = [
          {id: 'id', name: 'id', field: 'id'},
          {id: 'number', name: 'number', field: 'number', exandLevel: 'all'},
          {id: 'string', name: 'string', field: 'string'},
          {id: 'date', name: 'date', field: 'date'},
          {id: 'time', name: 'time', field: 'time'},
          {id: 'bool', name: 'bool', field: 'bool'}
];
</pre>

"expandLevel" means what level of expando should this column show. The root level is 1, the second is 2, and so on. All other values for this parameter means "all levels". But since we haven't set "treeNested" to true, all we need is a true value here.

Now the expandoes are moved to the 2nd column:

![customize expando position](http://oria.github.io/gridx/tutor/image/gridx-14.png)

Just now I mentioned "treeNested" parameter, which might make you confused. This is parameter of Tree module, but you can directly declare it as grid parameter:

<pre>
var grid = new Grid({
    modules: ["gridx/modules/Tree"],
    <b>treeNested: true</b>
});
</pre>

By default this is set to false and all expandoes are put in one column. If it is set to true, then different levels of expandoes will be put in different columns:

![nested tree](http://oria.github.io/gridx/tutor/image/gridx-15.png)

Users can also explicitly declare which columns should the expandoes be shown:

<pre>
structure = [
          {id: 'id', name: 'id', field: 'id', <b>exandLevel: 1</b>},
          {id: 'number', name: 'number', field: 'number'},
          {id: 'string', name: 'string', field: 'string', <b>exandLevel: 2</b>},
          {id: 'date', name: 'date', field: 'date'},
          {id: 'time', name: 'time', field: 'time', <b>exandLevel: 3</b>},
          {id: 'bool', name: 'bool', field: 'bool'}
];
</pre>

As introduced before, the "id" column here will show root level expandoes, and the "string" column the second level, the "time" column the third:

![customize nested tree expando position](http://oria.github.io/gridx/tutor/image/gridx-16.png)

[to be continue...]


