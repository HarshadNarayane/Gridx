The module called **TitleBar** adds a title to the top of the table.  The text of the title is provided by the `label` attribute.
Be sure and AMD load the module called `gridx/modules/TitleBar`.

<pre>
var myGrid = new Grid({
   ...
   modules: [
      {
         "moduleClass": TitleBar,
         "label": "My table title"
      }
   ],
   ...
});
</pre>