Although also based on Dojo store, Gridx has a completely different architecture compared to DataGrid/EnhancedGrid. It has a pure non-UI core to perform all the logic grid data operations in a convenient and consistent way, a clean and intuitional set of API, and a flexible module system to not only make various grid features highly configurable, but also reduce the running code size when some functionalities are not used at all.

This article is a brief introduction on how to use Gridx. Gridx is still under development, and some APIs and implementation details are still up to change. But its basic usage and architecture is already stable.

![gridx demo](http://oria.github.com/gridx/tutor/image/gridx-1.png)

## 1. Creation
Assume we have an HTML page like this:
```html
<html>
<head>
    <title>Gridx Demo</title>
    <script type="text/javascript" src="dojo/dojo.js" data-dojo-config="async: true"></script>
</head>
<body>
    <!-- We'd like to show a grid here -->
    <div id="gridNode" style="width: 400px; height: 300px;"></div>
</body>
</html>

```
First, let's import the CSS file for Gridx:
```html
<link rel="stylesheet" href="gridx/resources/claro/Gridx.css" />
```

Currently only claro theme is available. If you need to run grid in RTL mode, please use the following instead:
```html
<link rel="stylesheet" href="gridx/resources/claro/Gridx_rtl.css" />
```

And another important step - GridX needs an explicit width/height setting:
```html
```

The JavaScript modules we must "require" are:<br />
1) A kind of store, for example: dojo/store/Memory<br />
2) gridx/Grid<br />
3) gridx/core/model/cache/Sync (for client store) or gridx/core/model/cache/Async (for server store)<br />
Assume we are creating a grid upon dojo.store.Memory, here's what we need:

<pre>
require([
	'dojo/store/Memory',
	'gridx/Grid',
	'gridx/core/model/cache/Sync'
], function(Store, Grid, Cache){
	var store = new Store({
		data: [
			{id: 1, title: 'Hey There', artist: 'Bette Midler'},
			{id: 2, title: 'Love or Confusion', artist: 'Jimi Hendrix'},
			{id: 3, title: 'Sugar Street', artist: 'Andy Narell'}
		]
	});
	......
});
</pre>
Similar to DataGrid/EnhancedGrid, column structure is also necessary:
<pre>
var columns = [
	{field: 'id', name: 'Identity'},
	{field: 'title', name: 'Title'},
	{field: 'artist', name: 'Artist'}
];
</pre>
OK, now everything's ready, we can create our simple grid:
<pre>
var grid = new Grid({
	cacheClass: Cache,
	store: store,
	structure: columns
}, 'gridNode');	//Assume we have a node with id 'gridNode'
grid.startup();
</pre>
Gridx inherits dijit._WidgetBase, so all the widget tricks are available. 

This grid is extremely basic but solid. Lots of modules can be added to it flexibly without blocking each other. We'll see how this works in later sections.

## 2. Basic APIs and API objects
Gridx introduced API objects (objects holding APIs): rows, columns and cells. So we can get the id of the second row by:
<pre>
var id = grid.row(1).id;
</pre>
Get the data of cell at the first row second column by:
<pre>
var data = grid.cell(0, 1).data();
</pre>
Get the name of the third column by:
<pre>
var title = grid.column(2).name();
</pre>
Here the return values of function `grid.row()`, `grid.cell()` and `grid.column()` are API objects. They are just a container of a bunch of APIs, so do not have status. That means, if we get a cell object by:
<pre>
var cell = grid.cell(0, 0);
var data1 = cell.data();
</pre>
Then we change the data in this cell, and call: 
<pre>
var data2 = cell.data();
</pre>
Then data2 will be the updated data instead of data1.

To get an array of all columns, there's a better API:
<pre>
var columns = grid.columns();
</pre>
`columns()` or `rows()` returns an array of API objects. They also accept ranges.
If you need only the first 3 columns, just write:
<pre>
grid.columns(0, 3);
</pre>
Two columns starting from the second one:
<pre>
grid.columns(1, 2);
</pre>
All columns starts from the third one:
<pre>
grid.columns(2);
</pre>

Modules can add more methods to API objects. For example, if a sort module is available, we will be able to write:
<pre>
grid.column(0).sort();
</pre>

Other useful basic APIs include:
<pre>
	grid.setColumns(columnStructure);		//Reset the column structure
	grid.setStore(store);		//Reset store
	grid.columnCount();	//Faster than grid.columns().length;
	grid.rowCount();	//Faster than grid.rows().length;
	grid.resize();	//This is what layout widgets (containers) need.
</pre>

Modules have module APIs, which are mixed in deeper levels, so they won't conflict with others and grid itself. More details on module APIs will be covered in the next section.

## 3. Modules
Almost all of the Gridx UI features are implemented as modules, even header, body (the rows), scroller (both vertical and horizontal), etc. But these are core modules, which means they are loaded by default. Non-core modules are similar to the "plugins" in EnhancedGrid, they can be loaded on demand.

A Gridx module can be just a plain dojo class, and can be put anywhere. But usually we inherit from a base class `gridx/core/_Module` and put them in `gridx/modules` directory.

To add a module to a grid instance, we first need to "require" its source code, then declare it when creating grid:
<pre>
require([
	'dojo/store/Memory',
	'gridx/Grid',
	'gridx/core/model/cache/Sync',
	'gridx/modules/SingleSort',	//Require module source code
	'gridx/modules/ColumnResizer'	//Require module source code
], function(Store, Grid, Cache, Sort, ColumnResizer){
	......
	var grid = new Grid({
		cacheClass: Cache,
		store: store,
		structure: columns,
		modules: [
			Sort,		//Declare modules in a grid instance
			ColumnResizer	//Declare modules in a grid instance
		]
	});
});
</pre>
Declaring modules for a grid instance is just putting all the module classes into an array. This is not only shorter but also more straightforward than an associative array (like "plugins" in EnhancedGrid), because the "required" classes (Sort and ColumnResizer) are now explicitly (instead of implicitly, as strings) used.
But when we want to pass some initial arguments to a module, we have to write this:
<pre>
	modules: [
		{
			moduleClass: ColumnResizer,
			detectWidth: 10
		},
		......
	]
</pre>
If this looks not cool to you, there's another option: declare as grid arguments:
<pre>
	var grid = new Grid({
		......
		modules: [
			ColumnResizer
		],
		columnResizerDetectWidth: 10
	});
</pre>
If a module argument is declared as a grid argument, it must be prefixed by the name of the module, so that different modules won't conflict in arguments.

Gridx modules are designed to be replaceable. That means, if you don't like a specific module, you can implement your own, without worrying too much about other parts of grid or the previous implementation of that module. All you must follow is the API set of the module, so that your implementation could cooperate naturally with other modules. This "API set" is identified by module name (yes, the same name prefixing module arguments when declared in grid). For example, module `gridx/modules/select/Row` has a name of "selectRow". This means it implements all the APIs of a "selectRow" module. There's another module `gridx/modules/extendedSelect/Row`, it also has name "selectRow", which means it implements the same API set. So when another module depends on the feature of row selection, it only depends on the API set, instead of a particular implementation.

Sometimes, a module might depend on other modules, for example, UI module "paginationBar" depends on a non-UI module "pagination". So when declaring the PaginationBar module, we also need to require a Pagination module (We don't need to declare it when creating grid, just "require" is enough). If we forgot to do this, an error message would be shown in the console:
<pre>
	Error: Forced/Required Dependent Module 'pagination' is NOT Found for 'paginationBar'
</pre>
and the grid (probably) would not render correctly.

This seems a little inconvenient, since in EnhancedGrid, all the dependent plugins can be automatically loaded. But this is an unavoidable result of flexibility, because in EnhancedGrid, we can't easily replace any plugin if it is already "required" by another plugin. Another good thing is, there will be less "invisible" source code loading.

Almost every module can be directly accessed through the grid object. For example:
<pre>
	grid.select.row.selectById(rowId);
	grid.select.column.selectById(colId);
	grid.sort.sort(colId);
	grid.columnResizer.setWidth(colId, 100);
</pre>
In this way, each module has its own namespace, so they will not conflict with each other.

A module can choose to expose what APIs to grid, and what APIs to API objects (row/column/cell). Note the module name should only be an identifier for the APIs exposed to grid, so that there can be less dependency, and the code will be easier to maintain.

Here's a list of currently supported (non-core) modules, more details will be included in future docs:
<pre>
1. CellWidget
2. ColumnLock
3. ColumnResizer
4. Dod
5. Edit
6. Focus
7. IndirectSelect
8. Menu
9. NestedSort
10. Persist
11. Printer
12. RowHeader
13. RowLock
14. SingleSort
15. SummaryBar
16. TitleBar
17. Toolbar
18. Tree
19. VirtualVScroller
20. Filter
21. FilterBar
22. Pagination
23. PaginationBar
24. move.Row
25. move.Column
25. dnd.Row
26. dnd.Column
26. select.Row
27. select.Column
28. select.Cell
29. extendedSelect.Row
30. extendedSelect.Column
31. extendedSelect.Cell
32. exporter.CSV
33. exporter.Table
</pre>