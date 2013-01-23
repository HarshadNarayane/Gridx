The Gridx UI is designed to be extensible, so that FilterBar, PaginationBar, RowHeader, SummaryBar, QuickFilter, etc can be added, and their height is taken into consideration so that the grid body height is adjusted automatically. This article focuses on horizontal bars on top or bottom of gridx, and introduces you a special module called "Bar". <br/><br/>
The Bar module locates at `gridx/modules/Bar`. It allows you to freely define how contents should be shown in the bars. Although we already have various bars like filter bar and pagination bar, they are all fixed in UI layout. What if we'd like to show a link-button pager together with a quick filter box? Or switch the postion of pager and page size selector?

![gridx bar demo](http://oria.github.com/gridx/tutor/image/gridx-2.png)

Now let's see how to achieve all these using the Bar module:

<pre>
     &lt;div id='grid' jsid='grid' data-dojo-type='gridx/Grid' data-dojo-props='
          cacheClass: "gridx/core/model/cache/Sync",
          store: store,
          structure: layout,
          barTop: [
               "gridx/support/Summary",
               "gridx/support/DropDownPager",
               {pluginClass: "gridx/support/QuickFilter", style: 'text-align: right;'}
          ]
          barBottom: [
                "gridx/support/LinkSizer",
                {pluginClass: "gridx/support/LinkPager", style: 'text-align: right;'}
          ],
          modules: [
               "gridx/modules/Pagination",
               "gridx/modules/Filter",
               "gridx/modules/Bar"
          ]
     '&gt;&lt;/div&gt;
</pre>

Of course all these resources must be required first. And let's assume the "store" and "structure" parameter are properly set. <br/><br/>
If you've created a gridx before or read [other tutors](https://github.com/oria/gridx/wiki/Introduction-to-Gridx), the above code should be quite self-explanatory. The `"gridx/modules/Bar"` must be declared in the `modules` parameter without a doubt because we are right now talking about it. The `"gridx/modules/Pagination"` and `"gridx/modules/Filter"` are necessary because our pager and quickfilter need them. (Note this is the good point of separating filter logic from filter bar, and paging logic from pagination bar.) <br/>

## barTop and barBottom
Now let's take a careful look at the `barTop` and the `barBottom` parameter. They are actually parameters of the Bar module, but declared as grid-level parameters, which is much more easier and cleaner to write (see Introduction to Gridx). They are all of array type declaring all the stuff you want on a bar, while `barTop` means "top" bar and `barBottom` "bottom" bar, without doubt. Actually they can be regarded as a `<tr>` of html `<table>` element. Every item in the array corresponds to a `<td>`. So the `barTop` in the above example means 3 `<td>`s and `barBottoms` means 2 `<td>`s. A natural extension to this system is to show multiple `<tr>`s. This is also very straight-forward and I'm quite sure you've already known how to do it without peeking at the following example:

![multiple rows in gridx bar](http://oria.github.com/gridx/tutor/image/gridx-3.png)

The code is (omitting unchanged parts):

<pre>
barTop: [
          [
               {pluginClass: "gridx/support/Summary", colSpan: 2, style: 'text-align: center;'}
          ],
          [
               "gridx/support/DropDownPager",
               {pluginClass: "gridx/support/QuickFilter", style: 'text-align: right;'}
          ]     
     ],
</pre>

Now the `barTop` represents a whole `<table>`, which includes several sub-arrays as `<tr>`s. <br/><br/>

## Bar Item Definition
Finally let's pay attention to what can be declared as an item (a `<td>`, in other words). We've already seen that an item can be a string (indicating a class name), and also a configuration object. The complete structure of the object is:

<pre>
{
     pluginClass: "path/to/class", // string
     colSpan: 3, // number
     rowSpan: 2, // number
     style: "text-align: center;", // string
     className: "...", // string
}
</pre>

Note that the `style` and `className` are set on the `<td>` (yes, there really exists a `<td>`). Actually this whole object will be passed to the constructor of the plugin class, so you can also add any plugin parameter here:

<pre>
{
     pluginClass: "gridx/support/LinkPager",
     visibleSteppers: 5  // this is a parameter of the LinkPager widget
}
</pre>

Besides string and configuration object, a constructor function is also okay:

<pre>
require([
     "gridx/support/Summary",
     ......
], function(Summary, ...){
     
     var grid = new Grid({
          ......
          barTop: [
               Summary,      // directly use the constructor function.
               { pluginClass: Summary }      // pluginClass also supports this.
          ]
          ......
     });
});
</pre>

This is extremely useful when using AMD. <br/><br/>

Up till now we are always delcaring plugin classes. The Bar module can also support widget instances:

<pre>
barTop: [
     new dijit.Toolbar({...}),         // pass an instance directly
     { plugin: new dijit.Toolbar({...}), style: 'color: red;' },     // declared in configuration object
     { plugin: "myToolbarID" }         // existing widget ID
]
</pre>

The problem for declaring instances is they can not be reused in multiple places. But classes can:

<pre>
var toolbar = new dijit.Toolbar({...});

barTop: [
     toolbar,
     toolbar     // WRONG! one instance can't appear in two places.
]

barTop: [
     "dijit/Toolbar",
     "dijit/Toolbar"     // this is okay!
]
</pre>

Last but not least, you can also define arbitray html contents in your bar. For example:

<pre>
barTop: [
     { content: "&lt;h1&gt;My Grid Title&lt;/h1&gt;", style: "color: blue;" }
]
</pre>

![Arbitrary html in gridx bar](http://oria.github.com/gridx/tutor/image/gridx-4.png)

And if a falsy value is declared as an item, a empty `<td>` will be inserted. This is useful for multiple bar rows.

<pre>
barTop: [
     [ Summary, QuickFilter ],
     [ null, LinkSizer]     // skip the first cell in this row
]
</pre>

## Modules Depending on Bar
In Gridx v1.1, all bar modules except filter bar are rewritten using this Bar module. For example, the `gridx/modules/pagination/PaginationBar` is now built up by `gridx/support/Summary`, `gridx/support/LinkPager` and `gridx/support/LinkSizer`. `gridx/modules/filter/FilterBar` is not likely to be broken up into several pieces so it is left untouched. And `gridx/modules/TitleBar` is now deprecated since the same effect can be easily achieved by Bar module, as previous sample shows.

That's all. Happy "bar"ing!