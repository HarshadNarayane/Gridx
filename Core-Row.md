The `Row` object is a core part of GridX.  It models a Row in the table.  Every row has an identifier which can be retrieved with the `id` property.

It has a number of methods including:

* `cell(...)`
* `cells(...)`
* `data()`
* `deselect()`
* `index()` - Retrieve the index of this row
* `isSelected()`
* `item()`
* `moveTo(...)`
* `node()`
* `parent()`
* `rawData()`
* `select()`
* `setRawData(rawData)`
* `visualIndex()` - Not clear how this differ from `index()`.  Github issue #42 has been raised to track.

A Row object can be retrieved through the GridX method called `row(rowId)`.