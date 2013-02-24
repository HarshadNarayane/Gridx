The `Menu` module provides support for context menus within the grid.  A context menu is shown with a right-click action upon the grid.  When the `Menu` module is added to the grid, a new object can be found at `grid.menu`.  This object exposes two methods for working with menus.
The first is called `bind(menuDijit, bindArgs)`.  The first parameter is an instance of a menu created by Dojo.  The second parameter describes how the menu is to be bound to the grid.  The is an object which can contain:
* `hookPoint` (`String`) - This may be one of `cell`, `header`, `headercell`, `row`, `body`, `grid`.
* `selected` (`Boolean`) - Should the menu be bound only to the selected items

When a menu item is fired, the `gridx.menu.context` property determines what it is that the menu is being applied to.  The properties contained in this are:

* `cell`
* `column` - Only set when `hookPoint` is `headercell`.
* `grid`
* `row`

It is not expected that all properties are populated.  Instead, the property populated will be a function of the `hookPoint` attribute in the menu options.