## New Features
### IndirectSelectColumn module
An alternative to the IndirectSelect module. Use a check box column instead of row header. Not relying on RowHeader module, so no misalignment issue. But will scroll with horizontal scroll bar, unless locked by ColumnLock module.
### VirtualScroll module
A helper module to make the grid scrollable on touch devices. Just scrollable, no "bouncing" or "sliding" effects yet.
### BidiSupport module
Special bidi support for individual columns. To make somes columns bidi, while others not.
### Initial FilterBar condition
Filter the grid when it is created (not after, so only one store fetch is needed).
### "enum" type for Filter Bar and Filter Dialog
Can provide a Select widget as the value box in filter dialog.
### Bar plugin as module
Can easily declare Bar plugins in a module, so that pagination bar, quick filter, summary bar, etc, can be implemented using exising Bar plugins.

### QuickFilter module
A pre-configured QuickFilter support widget, used in Bar module, placed at the top right cornor of grid.
### GridCommon class:
Just Grid class plus some frequently used modules.
### gridx/support directory
Moved from gridx/modules/barPlugins, to emporsize that they are actually not a part of the grid, but some supporting tools.

## Major Improvements:
### Tree module
Fixed some major bugs when using Tree module together with pagination, cell widget and other modules.
### FormatSort model extension
Also support new stores.
### API docs
Re-organized to make it easier to understand and look-up.

## Refactored Features
### PaginationBar module
Created by gridx/support/Summary, gridx/support/LinkPager and gridx/support/LinkSizer. Now just a shortcut.
### PaginationBarDD module
Created by gridx/support/Summary, gridx/support/DropDownPager and gridx/support/DropDownSizer. Now just a shortcut.
### SummaryBar module
Created by gridx/support/Summary. Now just a shortcut.
### ToolBar module
Created directly upon Bar module. Now just a shortcut.
### export and print
Refactored to be single functions for each exporter and printer, taking grid as an argument, and moved to gridx/support.

## Deprecated Features:
* TitleBar module: 1.2. Use Bar module instead.
* exporter modules: 1.2. Use gridx/support/exporter instead
* Printer module: 1.2. Use gridx/support/printer instead
* gridx/modules/barPlugins directory: 1.2. Use gridx/support directly instead

## Experimental Features:
* TouchScroll module
* TouchVScroller module
* RowLock module
* Tree module

## Other Changes:
* Add Focus to core modules
* Remove module dependency dead-lock check
* Change HOME/END key to move focus to the first/last cell in the current row
* A lot of fixes



