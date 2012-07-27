If there are a lot of rows to be shown, say 1000 rows, it will not be a good idea to render them out all at once, as long as your grid is not very height. That's why we need "virtual scrolling", a "lazy rendering" technique of rendering only some of the rows, and not rendering others until the vertical scroll bar is scrolled to a different position.

# 1. How to Use Virtual Scrolling?
In Gridx, "virtual scrolling" is an optional module, because there are many ways to implement "lazy rendering", for example "pagination".

In order to turn "virtual scrolling" on for your Gridx, you only need to require and declare the `VirtualVScroller` module:

	require([
		......
		//Require the file of this module.
		"gridx/modules/VirtualVScroller"
		......
	], function(..., VirtualVScroller, ...){

		......

		var grid = new Grid({
			id: 'grid',
			cacheClass: Cache,
			store: store,
			structure: structure,
			modules: [
				//Declare this module in the "modules" parameter.
				VirtualVScroller
			]
		});

		......
	});

That's it. Now your grid will only render the visible rows in the current view.

# 2. Difference from DataGrid
This "virtual scrolling" implementation is totally different from the one in DataGrid in the following aspects:

## a) The scroll bar and the grid body part are separated.
In DataGrid, the scroll bars are native. So when virtual scrolling, the native reponse of the scroll bar is always faster than the rendering speed of the rows in grid body. That's why we can see "blank space" when scrolling DataGrid.
In Gridx, with scroll bar and grid body separated, the refreshing of grid body is delayed, so there will be no "blank space" any more, no matter how fast you scroll.

## b) The grid body is rendered in the unit of "rows" instead of "pages".
In DataGrid, all the rows are cut into several pages, every page in a separate DOM node. These page nodes are absolutely positioned in the grid body node, so every time a row has changed its height, all the following pages need to be re-positioned, which is very inefficient.
In Gridx, there's no "page" at all. The grid body is rendered directly in the unit of "rows". Row nodes are queued in the body node with out special CSS styles, so the browser will automaically handle the change of row heights. And also, you will never need to remember the parameters of "rowsPerPage" and "keepRows" any more.

## c) The grid can show a huge number of rows, regardless of the limitation of DOM node height.
If a DOM node is too high, browsers can not show it correctly. In FireFox, this limitation is 17895697px, in WebKit, 134217726px, both are big enough to contain tens of thousands of rows. But in IE, this limitation is as small as 1342177px. If there are 30000 rows and each row is 50px high, the browser won't be able to show it.
Here in the "virtual scrolling" of Gridx, the body only show the visible rows, its height is not related to the appearance of the scroller bar. So a simple transform can be done when there are too many rows. This makes it possible for Gridx to show any count of rows.

# 3. Configurable "buffSize"
There's an property of this VirtualVScroller module: buffSize, which determines the count of rows to be rendered above or below the current view. Setting this buffSize to a bigger value will reduce the rate of rendering new rows, but will also make the grid render more rows each time. If you feel it a little hard to scroll the grid, you can try setting it to 10 or 20 or higher. The default value is 5, which means for every view, there are 5 rows above the view and 5 rows below the view are also rendered, and it will not triger the render logic if you just scroll a little bit.

To set it when creating Gridx, pass vScrollerBuffSize as an argument to Gridx, where vScroller is the name of the VirtualVScroller module:

	var grid = new Grid({
		......
		vScrollerBuffSize: 10,
		......
	});

You can also set it after the grid is created (it is a read/write property), and it'll take effect in the next scroll:

	grid.vScroller.buffSize = 10;

or:

	grid.vScrollerBuffSize = 10;

Both are right.