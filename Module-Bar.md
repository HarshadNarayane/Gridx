The Bar module provides support for two bars.  One above the table and one below.  You don't need to show both if you don't want to.  When defining the grid and having included the `gridx/modules/Bar` package, a new property pair will be available on the grid's definition.  These properties are:

* `barTop` - definitions for the top bar
* `barBotton` - definitions for the bottom bar

Both of these are of type "Array of Objects".  The definition of an object is:

* pluginClass
* colSpan
* style:
* className
* plugin - A Dijit Widget instance
* content - HTML which will be inserted

See also:
* [How to add bars to gridx?](https://github.com/oria/gridx/wiki/How-to-add-bars-to-gridx%3F-%5Bv1.1%5D)