There are many ways to implement sorting in Gridx. Let's start with the simplest `SingleSort` module.

To use this module, just require and declare it, as usual:

	require([
		......
		//Require the file of this module.
		"gridx/modules/SingleSort"
		......
	], function(..., SingleSort, ...){

		......

		var grid = new Grid({
			id: 'grid',
			cacheClass: Cache,
			store: store,
			structure: structure,
			modules: [
				//Declare this module in the "modules" parameter.
				SingleSort
			]
		});

		......
	});

The `SingleSort` module makes it possible to sort a column by clicking its header cell. And it also provides some useful APIs, so that the sorting work can also be done programmatically:

	grid.sort.sort('someColumnID', true);

The first argument is a column ID, and the second is whether to sort descendingly. These inputs will simply be wrapped into an object array, like [{attribute: 'someColumnField', descending: true}], and passed to the `fetch` or `query` method of the store.

Maybe you'll think this `sort.sort()` is ugly. Yes it is one of few APIs that does not benefit much from the hierarchical API design. But you can always do the sorting work in another way:

	grid.column('someColumnID').sort(true);

It first get the column then sort it. Looks better? Using this API you can also sort a column by its index:

	grid.column(1).sort(true);

The UI design of this SingleSort module didn't provide a way to clear sorting using mouse or keyboard. So we have to do it programmatically, if necessary:

	grid.sort.clear();

Some UI designer will require putting a clear-sort button in a toolbar or somewhere, and this is the line you need to put in its onclick event handler.

By definition, this `SingleSort` module can only sort one column at one time. If you click an unsorted column header, it'll automatically clear the previously sorted column, and sort the new one. To sort multiple columns in a nested style (that is, the tfirst column is the main order, the second one only sorts the rows that are equal in the first column, and so on), you can use the `gridx/modules/NestedSort` module:

		var grid = new Grid({
			id: 'grid',
			cacheClass: Cache,
			store: store,
			structure: structure,
			modules: [
				//Declare this module in the "modules" parameter.
				NestedSort
			]
		});

This module also has the name "sort", so it can NOT coexist with the `SingleSort` module, which is also quite reasonable. If you declare both of them in one grid, the latter will replace the former. This `NestedSort` module also implements APIs like sort() and clear(), except its sort() method expects an array of columns to be sorted:

	grid.sort.sort([{ colId: 'someColumnID', descending: true }, { colId: 'someOtherColumnID', descending: false }]);

Both of these modules share the same logical part: the `Sort` model extension. Sounds complicated? Well, in most cases you don't need to know it, except you want to do something special. 
Since the sorting work is done in the store, so it is the functionality of grid data model instead of its UI (modules). That means, you can always sort a grid, even you don't have any of these sorting modules:

	var grid = new Grid({
		id: 'grid',
		cacheClass: Cache,
		store: store,
		structure: structure,
		//Declare the Sort model extension, if no modules are declaring it for you.
		modelExtensions: [
			"gridx/core/model/extensions/Sort"
		]
	});

	......

	grid.model.sort([{ colId: 'someColumnID', descending: true }]);
	grid.body.refresh();

The only thing that needs to be remembered is to refresh the body after the grid model is altered. And of course, you won't see any triangles in the header cell marking that column as sorted, that's what the sort UI modules do.

Sort modules are very simple, but there're still 2 parameters that need some discussion:

### 1. initialOrder
When this parameter is provided when creating Gridx, the corresponding column will appear sorted as soon as the grid is created. That means you'll see the little triangles in the column header as well as the sorted data. For example:
	
		var grid = new Grid({
			id: 'grid',
			cacheClass: Cache,
			store: store,
			structure: structure,

			//Declare initialOrder as grid parameter:
			sortInitialOrder: { colId: 'someColumnID', descending: true },

			modules: [
				//Declare sort module in the "modules" parameter.
				SingleSort
			]
		});

or

		var grid = new Grid({
			id: 'grid',
			cacheClass: Cache,
			store: store,
			structure: structure,
			modules: [
				//Declare sort module in the "modules" parameter.
				{
					moduleClass: SingleSort,
					//Declare initialOrder as module parameter:
					initialOrder: { colId: 'someColumnID', descending: true }
				}
			]
		});

Both ways work, because `initialOrder` is a module paramter. That also means, If you implement a sort module that does not support it, it'll be of no use.

### 2. baseSort
This is a parameter of the Sort model extension. So it is only a grid parameter, and you don't need to put a "sort" string in front of it.

		var grid = new Grid({
			id: 'grid',
			cacheClass: Cache,
			store: store,
			structure: structure,
			//Declare base sort order, note that this must be an array:
			baseSort: [{ colId: 'someColumnID', descending: true }],
			modules: [
				//Declare sort module in the "modules" parameter.
				SingleSort
			]
		});

If this parameter is provided, the grid will also appear sorted when created, but there will be no little triangles shown in the column header. And you can never clear the sort. The data in grid will appear that this is its "base" order, and you can only sort on the base of it, and clear back to it.

## Preventing a column from being sorted
It is not uncommon that we want to prevent a column from being able to be selected for sorting.  For example, if we have a column that contains a graphic or a button, it usually doesn't make sense to sort on that column.  Even if there is no change to apparent row order, it may still show that column as the current one sorted.

When we include SingleSort as a module, a new property is available in the column definition specification.  That property is called `sortable` and is a boolean.  If we set it to `false` (the default is `true`) then the column will no longer be eligible to be sorted.(Note: (from: Max Shalima) : to set this property i MUST use **setSortable **method otherwise you will not see any effect.