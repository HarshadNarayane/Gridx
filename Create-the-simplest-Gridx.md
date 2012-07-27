The simplest gridx does not have any extra modules or extensions, and it is fed by a client store. It can do hardly no more than a native html table, but it's a solid base and a good start for advanced features. That's why we call it Gridx, an extensible grid.

# 1. Preparation

There are 5 things to be prepared before creating a Gridx. Well, believe me, I'm just trying to be precise here. You'll take these stuff for granted if you are familiar with Dojo.

## a) A store
Create a store from dojo/store/*, dojo/data/*, or dojox/data/*.
Gridx, inheriting the idea of DataGrid, is just a view of the store. So create one, for example, like this:

	//Use dojo/store/Memory here
	var store = new Memory({
		data: [
			{ id: 1, name: 'John', score: 130, city: 'New York', birthday: '1980/2/5'},
			{ id: 2, name: 'Alice', score: 123, city: 'Washington', birthday: '1984/3/7'},
			{ id: 3, name: 'Lee', score: 149, city: 'Shanghai', birthday: '1986/10/8'},
			{ id: 4, name: 'Mike', score: 100, city: 'London', birthday: '1988/8/12'},
			{ id: 5, name: 'Tom', score: 89, city: 'San Francisco', birthday: '1990/1/21'}
		]
	});

A Memory store is a client side store, which is as simple as an array with some easy to use APIs.

## b) A column structure
Define the columns in an array. A column can be a direct mapping of a field in store, which is the most common case. If this "field" is not provided, there should be something else to tell the grid what to show in that column.

	var structure = [
		{ id: 'name', field: 'name', name: 'Name', width: '50px'},
		{ id: 'city', field: 'city', name: 'City'},
		{ id: 'score', field: 'score', name: 'Score', width: '80px'}
	];

It's a good practice to provide a meaningful unique ID for every column, so that you can uniquely refer to a column at anytime, no matter where it is moved or how it is modified. If omitted, the grid will use "1", "2", "3", ... as IDs for columns.

## c) A cache implementation
Server side stores and client side store are totally different, so different logics are provided to handle them. If you are using only a simple client store, like Memory, there's no need to do any lazy-loading stuff. So tell the grid what kind of store you are using by providing different cache implementations.

	//We are using Memory store, so everything is synchronous.
	var cacheClass = "gridx/core/model/cache/Sync";

By the way, do forget to "require" this class, the grid won't automatically load it for you.

If you are using AMD style of coding, it's also a good practice to assign the required cache to an argument, and use that argument directly:

	require([
		......
		"gridx/core/model/cache/Sync",
		......
	], function(..., Cache, ...){
		
		......

		var cacheClass = Cache;

		......
	});

## d) CSS files

Import CSS files of Gridx into your html file:

	<style type="text/css">
		//Use claro theme.
		@import "your/path/to/dijit/themes/claro/claro.css";
		@import "your/path/to/dijit/themes/claro/document.css";

		//Import the main CSS file of Gridx.
		@import "your/path/to/gridx/resources/claro/Gridx.css";

		//import RTL css if you need to support rtl.
		@import "your/path/to/gridx/resources/claro/Gridx_rtl.css";
	</style>

## e) Grid width and height
Gridx does not have a default width or height, which is different from the old DataGrid. So you need to provide them, in anyway you like. For example, in CSS:

	.gridx {
		width: 400px;
		height: 200px;
	}


# 2. Creation
When everything is prepared, we can start creating our grid:

	require([
		//Require resources.
		"dojo/store/Memory",
		"gridx/core/model/cache/Sync",
		"gridx/Grid"
	], function(Memory, Cache, Grid){

		var store = //create store......
		var structure = //create structure......

		//Create grid widget.
		var grid = Grid({
			id: 'grid',
			cacheClass: Cache,
			store: store,
			structure: structure
		});

		//Put it into the DOM tree. Let's assume there's a node with id "gridContainer".
		grid.placeAt('gridContainer');

		//Start it up.
		grid.startup();
	});

Never forget to call grid.startup(), since the column width calculation and layout rendering need to access the geometry information of grid DOM nodes.

# 3. Run the page
OK, now we are done.
When you run the page, you'll see a 400px by 200px grid, with header and body parts, showing the data in your store.
Too simple? Let's add more things into it in other tutorials.