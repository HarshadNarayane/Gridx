The CellWidget module allows a Cell to contain a Dojo Widget (Dijit).  The `decorator` may return HTML which is parsed by the Dijit parser to create the widget.  For exanmple

<pre>
decorator: function(){
   return [
      '&lt;span data-dojo-type="dijit.form.CheckBox" ',
      'data-dojo-attach-point="cb" ',
      'data-dojo-props="readOnly: true"',
      '&gt;&lt;/span&gt;',
   ].join('');
},
</pre>
would create a checkbox widget.
To set a value within the widget, the column descriptor has a method called `setCellValue(data)` added to it.  When this function is called, it is the responsibility of the function to set the widget to contain the data value.  Notice that in the widget description, we can add the `data-dojo-attach-point` option.  This creates a variable which can be accessed in the `setCellValue` callback through `this.<variableName>`.  This will be the object reference to the new widget.