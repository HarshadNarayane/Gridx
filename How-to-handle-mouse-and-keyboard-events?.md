All the following mouse and keyboard events are supported for different parts of gridx:

<table>
<tr><th></th><th>MouseOver</th><th>MouseOut</th><th>MouseDown</th><th>MouseUp</th><th>Click</th><th>DblClick</th><th>ContextMenu</th><th>KeyDown</th><th>KeyUp</th><th>KeyPress</th></tr>
<tr><td>Header</td><td>Y</td><td>Y</td><td>Y</td><td>Y</td><td>Y</td><td>Y</td><td>Y</td><td>Y</td><td>Y</td><td>Y</td></tr>
<tr><td>HeaderCell</td><td>Y</td><td>Y</td><td>Y</td><td>Y</td><td>Y</td><td>Y</td><td>Y</td><td>Y</td><td>Y</td><td>Y</td></tr>
<tr><td>Row</td><td>Y</td><td>Y</td><td>Y</td><td>Y</td><td>Y</td><td>Y</td><td>Y</td><td>Y</td><td>Y</td><td>Y</td></tr>
<tr><td>Cell</td><td>Y</td><td>Y</td><td>Y</td><td>Y</td><td>Y</td><td>Y</td><td>Y</td><td>Y</td><td>Y</td><td>Y</td></tr>
<tr><td>RowHeaderHeader</td><td>Y</td><td>Y</td><td>Y</td><td>Y</td><td>Y</td><td>Y</td><td>Y</td><td>Y</td><td>Y</td><td>Y</td></tr>
<tr><td>RowHeaderCell</td><td>Y</td><td>Y</td><td>Y</td><td>Y</td><td>Y</td><td>Y</td><td>Y</td><td>Y</td><td>Y</td><td>Y</td></tr>
</table>


The naming convertion is: "on" + regionName + eventName. 
For example: "onCellClick", "onHeaderCellMouseOver", "onRowKeyDown", "onRowHeaderHeaderClick".

Note the "RowHeaderHeader" and "RowHeaderCell" events are only available when the gridx/modules/RowHeader is used.

To add handler for any of these events, just connect it on the grid object:

<pre>
grid.connect(grid, "onCellMouseOver", function(evt){
     // your handler logic here.
});
</pre>

Note it is a good practice to use `grid.connect` instead of `dojo.connect`, because grid as a widget can automatically disconnect all the event connections when it is destroyed, so that memory leak can be avoided.

An event object is passed to your handler function, in which several useful properties are already available:

<table>
<tr><th></th><th>Description</th><th>Available on</th></tr>
<tr><td>evt.rowId</td><td>The ID of the current row</td><td>Cell, Row, RowHeaderCell</td></tr>
<tr><td>evt.rowIndex</td><td>The index of the current row under its parent</td><td>Cell, Row, RowHeaderCell</td></tr>
<tr><td>evt.parentId</td><td>The ID of the parent row of the current row</td><td>Cell, Row, RowHeaderCell</td></tr>
<tr><td>evt.visualIndex</td><td>The visual index of the current row</td><td>Cell, Row, RowHeaderCell</td></tr>
<tr><td>evt.columnId</td><td>The ID of the current column</td><td>Cell, HeaderCell</td></tr>
<tr><td>evt.columnIndex</td><td>The index of the current column</td><td>Cell, HeaderCell</td></tr>
<tr><td>evt.cellNode</td><td>The DOM node of the current cell</td><td>Cell</td></tr>
<tr><td>evt.headerCellNode</td><td>The DOM node of the current header cell</td><td>HeaderCell</td></tr>
<tr><td>evt.rowHeaderCellNode</td><td>The DOM node of the current row header cell</td><td>RowHeaderCell, Row (when isRowHeader is true)</td></tr>
<tr><td>evt.isRowHeader</td><td>Whether this event is fired from row header</td><td>RowHeaderCell, Row (when isRowHeader is true)</td></tr>
</table>

With the above information, you can easily get whatever you want. For example:

<pre>
var cell = grid.cell(evt.rowId, evt.columnId, true);
var cellData = cell.data();
var isRowSelected = cell.row.isSelected();
var headerName = cell.column.name();
</pre>
