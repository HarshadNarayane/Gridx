The module called **TitleBar** adds a title to the top of the table.  The text of the title is provided by the `label` attribute.  This module has been deprecated by the `Bar` module.
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

#### See also
* [Bar module] (https://github.com/oria/gridx/wiki/Module-Bar)