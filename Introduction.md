Although also based on Dojo store, Gridx has a completely different architecture compared to DataGrid/EnhancedGrid. It has a pure non-UI core to perform all the logic grid data operations in a convenient and consistent way, a clean and intuitional set of API, and a flexible module system to not only make various grid features highly configurable, but also reduce the running code size when some functionalities are not used at all.

This article is a brief introduction on how to use Gridx. Gridx is still under development, and some APIs and implementation details are still up to change. But its basic usage and architecture is already stable.

## 1. Creation
Assume we have an HTML page like this:
<pre>
&lt;html&gt;
&lt;head&gt;
	&lt;title&gt;Gridx Demo&lt;/title&gt;
	&lt;script type=&quot;text/javascript&quot; src=&quot;dojo/dojo.js&quot; data-dojo-config=&quot;async: true&quot;&gt;&lt;/script&gt;
&lt;/head&gt;
&lt;body&gt;
	&lt;!-- We&#039;d like to show a grid here --&gt;
	&lt;div id=&quot;gridNode&quot; style=&quot;width: 400px; height: 300px;&quot;&gt;&lt;/div&gt;
&lt;/body&gt;
&lt;/html&gt;
</pre>
First, let's import the CSS file for Gridx:
<pre>
&lt;link rel="stylesheet" href="gridx/resources/claro/Gridx.css" /&gt;
</pre>
Currently only claro theme is available. If you need to run grid in RTL mode, use the following instead:
<pre>
&lt;link rel="stylesheet" href="gridx/resources/claro/Gridx_rtl.css" /&gt;
</pre>

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

## 2. Basic APIs
Gridx introduced API objects: rows, columns and cells. So we can get the id of the second row by:
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
Here the return values of function grid.row(), grid.cell() and grid.column() are API objects. They are just a container of a bunch of APIs, so do not have status. That means, if we get a cell object by:
<pre>
var cell = grid.cell(0, 0);
var data1 = cell.data();
</pre>
Then we change the data in this cell, and call: 
<pre>
var data2 = cell.data();
</pre>
Then data2 will be the updated data instead of data1.

To get an array of column names, there's a better API:
<pre>
var names = grid.columns().map(function(col){
	return col.name();
});
</pre>
columns() or rows() returns an array of API objects. They also accept ranges.
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
The returned array has been mixed in all the useful array functions like map, filter, forEach, some and every.

Modules can add more methods to API objects. For example, if a sort module is available, we will be able to write:
<pre>
grid.column(0).sort();
</pre>

Other users basic APIs include:
<pre>
	grid.setColumns(columnStructure);		//Reset the column structure
	grid.setStore(store);		//Reset store
	grid.columnCount();	//Faster than grid.columns().length;
	grid.rowCount();	//Faster than grid.rows().length;
	grid.resize();	//This is what layout widgets (containers) need.
</pre>

Modules have module APIs, which are mixed in deeper levels, so they won't conflict with others and grid itself. More details on module APIs will be covered in the next section.

3. Modules