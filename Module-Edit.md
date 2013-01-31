The Edit Module allows for editing of cells within the grid.

When a grid includes the Edit module, this does not mean that all the cells are immediately editable.  Instead, we must tell the grid which columns contain editable fields.  We do this by setting the column property called `editable` to `true`.  The default is `false` which means that cells in that column are not editable.

When a cell is to be edited, a new instance of Dojo widget (Dijit) is created at the location of the cell on the screen.  This widget is responsible for showing the current value and allowing the user to change that value.  By default, the widget used is an instance of `dijit.form.TextBox` however different widget types can be used.  The property called `editor` should be set to the String name of the Dijit class to be used.  Remember to define an AMD include of this class type if it is used.

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