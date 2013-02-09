The module called **Select Row** provides a mechanism for row selection.  This module is commonly used in conjunction with `RowHeader`.  When that module is included, a column is added to the left of the grid which allows selection of rows as a whole.

A module argument called `triggerOnCell` takes a boolean value.  By default it is false.  If set to true, clicking in any cell in a row selects the row as a whole.

The following methods are added to the row object:

* `select()`
* `deselect()`
* `isSelected()`

The following arguments are available to the SelectRow module:

* triggerOnCell (Boolean) - Should a click in a cell in the row select the row?

When a new row is selected, the `onSelected(row, rowid)` method of the select.row object is called.  Similarly, a `onDeselected(row, rowid)` method is available when a row is deselected.  The `row` parameter is a reference to the selected row object itself.

This `select/Row` object can be referenced by `<myGridX>.select.row`.  This object has some methods upon it which include:

* `getSelected()` - Returns an array of selected row ids.
* `isSelected(rowId)` - Returns `true` if the row described by `rowId` is already selected and `false` otherwise.