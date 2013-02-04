The Edit Module allows for editing of cells within the grid. Be sure and AMD include `gridx/modules/edit`.

When a grid includes the Edit module, this does not mean that all the cells are immediately editable.  Instead, we must tell the grid which columns contain editable fields.  We do this by setting the column property called `editable` to `true`.  The default is `false` which means that cells in that column are not editable.

When a cell is to be edited, a new instance of Dojo widget (Dijit) is created at the location of the cell on the screen.  This widget is responsible for showing the current value and allowing the user to change that value.  By default, the widget used is an instance of `dijit.form.TextBox` however different widget types can be used.  The property called `editor` should be set to the String name of the Dijit class to be used.  Remember to define an AMD include of this class type if it is used.  Another column property called `editorArgs` can be used to supply properties to the widget named in `editor`.  The `editorArgs` property is an object which the following properties:

* `props` (`String`) - Set of properties defined on the Dijit Widget
* `fromEditor` (`function(storeData, gridData)`)
* `toEditor` (`function(storeData, gridData, cell, editor)`)
* `constraints` (`Object`) - Additional properties passed to the editor.
* `useGridData` (`Boolean`) - Should the editor be fed with data from the Store or from the Grid?  The default is `false` which means to use the store data.  This property is not used if `toEditor` is supplied.
* `valueField` (`String`) - The property of the editor that holds the value.  This is normally `value` which is the default.

When the edit of the cell has finished, the data entered is written back into the store.  We can change how this is achieved by providing a function to be called to apply the change using our own logic.  The property for this is `customApplyEdit` which is a function with the signature `function(cell, value)`.  It is the responsibility of the code to set the value of the cell to be the value passed in as a parameter.

Here is an example of a simple editable grid

<pre>
var layout = [
   {
      ...
      "editable": true
      ...
   }
];
var grid = new Grid({
   ...
   "structure": layout,
   "modules": [Edit],
   ...
});
</pre>

By adding Edit to your grid, the `cell` object has some additions included:

* `editor()` - returns the Dijit widget that is used to display the editing

Events are also added including

* `onBegin(cell)` - called when editing begins.
* `onApply(cell, applySuccess)` - called after the changes made by editing have been applied.  `applySuccess` is true if the application of the changes was succesful.
* `onCancel(cell)` - Called when editing of the cell is canceled.

To add an event handler, the following may be used:
<pre>
myGrid.edit.connect(myGrid.edit, "onBegin", function(cell) {...});
</pre>