| Operation| keyboard| Required modules  |Description|
| ------------- |-------------| -----|-----|
|Undo	|	CTRL+Z	|	Edit	|	When focus is on gridx, press the key to undo last change	|
|Redo	|	CTRL+Y	|	Edit	|	When focus is on gridx, press the key to redo next change	|
|Save	|	CTRL+S	|	Edit	|	When focus is on gridx, press the key to save all the changes	|
|Change editing status	|	ENTER	|	Edit	|	When focus is on gridx, press the key to:<br>1.	apply current edit in editing statusenter<br>2.	editing status when in non-editing 1. status |
|Select Row	|	SPACE	|	select.Row / extendedSelect.Row	|	when focus is on a cell and **triggerOnCel** is true, press the key to select the current row	|
|Selct Columnn	| SPACE/ENTER	|	select.column / extendedSelect.column	|	When focus is on a specific column header cell, press the key to select current column. |
|Select Cell	|	SPACE	|	select.cell / extendedSelect.cell	|	Select current cell	|
|Toggle sort	|	ENTER	|	SingleSort	|	When focus is on a specific column header cell, press the key to sort.|
|Toggle sort	|	ENTER	|	NestedSort	|	When focus is on the nested sort button of a specific column header cell, press the key to toggle betweetn DESC, ASC sort and no sort|
|Enlarge column width	|	CTRL+SHIFT+->	|	ColumnResizer	|	When focus is on a specific column header, press the key **combination** to enlarge column width|
|Reduce column width	|	CTRL+SHIFT+<-	|	ColumnResizer	|	When focus is on a specific column header, press the key **combination** to reduce column width|
|Move focus	|	<- or ->	| Header	|	When focus is on a specific column header, press key to move focus	|
|Open/Close header menu|	ENTER	|HeaderMenu	|	Toggle between open or close header menu|
|Move focus	|	<- or ->	|	HeaderRegion	|	Move focus|
|Select all	|	SPACE	|	IndirectSelect	|	When focus is on the header of row header and the argument **all** is true, press the key to select all	|
|Select all	|	CTRL+A	|	IndirectSelect	|	When focus is on a specific row, press the key to select all	|
|Select all	|	SPACE	|	IndirectSelectColumn	|	When focus is on the cell of the header whose colid is **__indirectSelect__**, press the key to select all|
|Focus elements	|	F2	|	CellWidget	|	When focus is on the cell,	press the key to focus the first focusable element in the cell.	|
|Trigger Click	|	ENTER	|	PagedBody	|	When focus is on 'Load More' and 'Load Previous' button, press the key to trigger load more or load previous function.	|
|Move focus	|UP_ARROW & DOWN_ARROW	|	RowHeader	|	When focus is on a specific row header, press the key to move focuse up and down.	|
|Collapse/expand	|	CTRL + ->	|	Tree	|	When focus is on a specific cell node that has a collapse or expand icon, press the key combination to <br>1. expand tree in **LTR** mode<br>2. collapse the tree in **RTL** mode.|
|Collapse/expand	|	CTRL + <-	|	Tree	|	When focus is on a specific cell node that has a collapse or expand icon, press the key combination to <br>1. expand tree in **RTL** mode<br>2. collapse the tree in **LTR** mode.|
|Scroll	|	HOME	|	Tree	|	When focus is on grid vertical scroller bar, press the key to scroll to the first row.	|
|Scroll	|	END	|	Tree	|	When focus is on grid vertical scroller bar, press the key to scroll to the last row.	|
|Scroll	|	PgUp	|	Tree	|	When focus is on grid vertical scroller bar, press the key scroll up some rows.	|
|Scroll	|	PgDn	|	Tree	|	When focus is on grid vertical scroller bar, press the key scroll down some rows.|