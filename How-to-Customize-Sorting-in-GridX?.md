In Gridx, you can customize the sorting order of any column if you are using a client store, like dojo/store/Memory. But sorting is store's job, so by default this feature is not enabled in Gridx. To enable it, all you need is the model extension "FormatSort":

<pre>
var grid = new Grid({
    ......
    modelExtensions: [
           "gridx/core/model/extensions/FormatSort"  // Just like other modules, don't forget to require it first.
    ],
    ......
});
</pre>

"FormatSort" means it can sort "formatted" data (the data returned by the "formatter" function). You can set "sortFormatted: true" in column definition to enable this for a particular column. But at the same time this extension also provides the possibility to customize sort order. This is done by the "comparator" column parameter:

<pre>
structure: [
    { id: 'myColumn', field: 'date', name: 'Date', 
             comparator: function(v1, v2){
                  // let's assume data in this column are date strings. We need to transform them to Date object for comparison.
                   v1 = new Date(v1);
                   v2 = new Date(v2);
                   return v1 - v2;
             }
     }
]
</pre>

Note that this feature is only valid for client stores. If you are using a server store, the sorting job is done in server, Gridx can't help anymore.



