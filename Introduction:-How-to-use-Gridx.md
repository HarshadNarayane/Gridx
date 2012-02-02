# How to use Gridx

Although also based on Dojo store, Gridx has a completely different architecture compared to DataGrid/EnhancedGrid. It has a pure non-UI core to perform all the logic grid data operations in a convenient and consistent way, a clean and intuitional set of API, and a flexible module system to not only make various grid features highly configurable, but also reduce the running code size when some functionalities are not used at all.

This article is a brief introduction on how to use Gridx. Gridx is still under development, and some APIs and implementation details are still up to change. But its basic usage and architecture is already stable.

The source code of Gridx can be downloaded from GitHub. Now let's get started.

## 1. First Gridx

Experienced users of DataGrid/EnhancedGrid probably know that a grid instance has two major inputs: one is the data store, the other is the column structure. This is still true for Gridx:
`
var columns = [
    {field: 'id', name: 'Identity'},
    {field: 'title', name: 'Title'},
    {field: 'artist', name: 'Artist'}
];
var store = new Memory({...});
`
But in Gridx, we need one more thing: cache. 
Usually there're two types of stores: synchronous client side store and asynchronous server side store. In DataGrid/EnhancedGrid, they are regarded equal, so even you are using a pure client side store, you still can not get "unloaded" data (means not ever shown) directly from grid. And also, using complicated code for server side store to deal with a client store is quite wasteful. So we need to "tell" the grid what type of store we are using: Sync or Async.
This is achieved by "requiring" different "cache":
`
Require([
	'gridx/core/model/cache/Sync',		//For client store, usually we only need Sync cache
	//'gridx/core/model/cache/Async',		//For server store, we must use Async cache
	.......
], function(Cache){
	....
	//OK, thanks to amd, here Cache is ready for use.
});
`
"Cache" is the lowest level of code directly communicating with store. In most cases, a small client store is enough, so we don't need to even "load" any JavaScript code for server store into client memory.
OK, now we have everything necessary, let's create our first Gridx. Oh, wait, one more thing, the CSS file for Gridx is located in gridx/resources/claro/Gridx.css, and don't forget to create a node with visible height and width in HTML to hold this grid. Also, we are using claro theme (the only theme for now) here. Hope we can use more themes in the future.
`
<html>
<head>
<title>Gridx</title>
<style>
@import "dijit/themes/claro/claro.css";
@import "gridx/resources/claro/Gridx.css";
#gridNode {
	width: 400px;
	height: 300px;
}
</style>
<script type="text/javascript" src="dojo/dojo.js" data-dojo-config="async: true, parseOnLoad: true"></script>
<script type="text/javascript">
require([
	'gridx/Grid',
	'gridx/core/model/cache/Sync',
	'dojo/store/Memory'
], function(Grid, Cache, Memory){
	var columns = [
		{field: 'id', name: 'Identity'},
		{field: 'title', name: 'Title'},
		{field: 'artist', name: 'Artist'}
	];
	var store = new Memory({
		data: [
			{id: 1, title: 'Hey There', artist: 'Bette Midler'},
			{id: 1, title: 'Love or Confusion', artist: 'Jimi Hendrix'},
			{id: 1, title: 'Sugar Street', artist: 'Andy Narell'}
		]
	});
	var grid = new Grid({
		cacheClass: Cache,
		structure: columns,
		store: store
	}, 'gridNode');
	grid.startup();
});
</script>
</head>
<body class="claro">
	<div id="gridNode"></div>
</body>
</html>
`

## 2. Adding Modules
The grid we just created seems boring: no sorting, no filter, no column resizer, no pagination... Now let's see how to make it more powerful.
Most grid features are implemented as modules, and placed in the gridx/modules/ directory. To use a module, just "require" it and then declare it when creating grid.
`
require([
	'gridx/modules/SingleSort',
	'gridx/modules/ColumnResizer',
	'gridx/modules/extendedSelect/Row',
	......
], function(Sort, ColumnResizer, SelectRow){
	.......
	var grid = new Grid({
		cacheClass: Cache,
		structure: columns,
		store: store,
		modules: [
			Sort,
			ColumnResizer,
			SelectRow
		]
	});
	........
});
`
Then try again, these features will be available.

[Fig 2]

The "modules" property is just an array, which is simpler to write, and easier to deal with than an associative array (object), compared to the EnhancedGrid's plugin system:
`
plugins: {
	filter: true,
	selector: true,
	pagination: true
}
`
But what if we want to pass some arguments to a module? In an associative array this is straightforward:
`
plugins {
	selector: {
		arg1: blabla
		arg2: blablabla
	}
}
`
But in an ordinary array, we have to write:
`
modules: [
	{
		moduleClass: SelectRow,
		arg1: blabla,
		arg2: blablabla
	}
]
`
If every module has some arguments, it will become even more inconvenient.
So Gridx also supports another way to pass module arguments, that is, we can declare module arguments just as grid arguments:
`
var grid = new Grid({
	......
	modules: [
		SelectRow	//still simple
	],
	selectRowArg1: blabla,		//Now they are grid arguments
	selectRowArg2: blablabla
});
`
To distinguish different modules, an argument must be prefixed with its module name. Every module must have a "name", and different modules can share one name. Note that this module "name" is slightly different from the plugin "name" in EnhancedGrid. More details on module name will be explained in future sections.
This way of declaring module arguments has extra benefits except shorter. One day we will have a full list of grid arguments, including all module arguments in grid argument form, then we won't need to remember which argument is for which module anymore.

3. Basic APIs and Module APIs
4. No Complicated Structure? Seriously?
5. Column
6. Row
7. Gridx Model
