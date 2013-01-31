Usually, Gridx has a user-defined fixed height, and a vertical scroll bar will be shown when the number of rows overflow the body.

But sometimes, it is desirable that the grid show every row directly, without the scrollbar. Now you need a Gridx property called `autoHeight`:

	var grid = new Grid({
		......
		autoHeight: true,
		......
	});

Now the grid's height is not determined by the CSS styles. It is determined by the sum of the height of all rows in the current view. As a consequence, there will never be a vertical scroll bar. And when you scroll the mouse wheel over the grid body, the whole web page will be scrolled.

By definition, if `autoHeight` is true, there's no need to use the `VirtualVScroller` module, because all rows must be visible at the same time. But if you still use it, it will not give you any error message, and it will not do any harm, it's just your runtime code that gets bigger.