Although also based on Dojo store, Gridx has a completely different architecture compared to DataGrid/EnhancedGrid. It has a pure non-UI core to perform all the logic grid data operations in a convenient and consistent way, a clean and intuitional set of API, and a flexible module system to not only make various grid features highly configurable, but also reduce the running code size when some functionalities are not used at all.

This article is a brief introduction on how to use Gridx. Gridx is still under development, and some APIs and implementation details are still up to change. But its basic usage and architecture is already stable.

## 1. Creation
Assume we have an HTML page like this:
<pre>
&lt;html&gt;
&lt;head&gt;
	&lt;title&gt;Gridx Demo&lt;/title&gt;
	&lt;script type=&quot;text/javascript&quot; src=&quot;dojo/dojo.js&quot; data-dojo-config=&quot;async: true&quot;&gt;&lt;/script&gt;
&lt;/head&gt;
&lt;body&gt;
	&lt;!-- We&#039;d like to show a grid here --&gt;
	&lt;div id=&quot;gridNode&quot; style=&quot;width: 400px; height: 300px;&quot;&gt;&lt;/div&gt;
&lt;/body&gt;
&lt;/html&gt;
</pre>
First, let's import the CSS file for Gridx:
<pre>
&lt;link rel="stylesheet" href="gridx/resources/claro/Gridx.css" /&gt;
</pre>
Currently only claro theme is available. If you need to run grid in RTL mode, use the following instead:
<pre>
&lt;link rel="stylesheet" href="gridx/resources/claro/Gridx_rtl.css" /&gt;
</pre>

The JavaScript modules we must "require" are:
1. A kind of store, for example: dojo/store/Memory
1. gridx/Grid
1. gridx/core/model/cache/Sync (for client store) or gridx/core/model/cache/Async (for server store)
Assume we are creating a grid upon dojo.store.Memory, here's what we need:

<pre>
require([
	'dojo/store/Memory',
	'gridx/Grid',
	'gridx/core/model/cache/Sync'
], function(Store, Grid, Cache){
	var store = new Store({
		data: [
			{id: 1, title: 'Hey There', artist: 'Bette Midler'},
			{id: 2, title: 'Love or Confusion', artist: 'Jimi Hendrix'},
			{id: 3, title: 'Sugar Street', artist: 'Andy Narell'}
		]
	});
	......
});
</pre>
Similar to DataGrid/EnhancedGrid, column structure is also necessary:
<pre>
var columns = [
	{field: 'id', name: 'Identity'},
	{field: 'title', name: 'Title'},
	{field: 'artist', name: 'Artist'}
];
</pre>
OK, now everything's ready, we can create our simple grid:
<pre>
var grid = new Grid({
	cacheClass: Cache,
	store: store,
	structure: columns
}, 'gridNode');	//Assume we have a node with id 'gridNode'
grid.startup();
</pre>
Gridx inherits dijit._WidgetBase, so all the widget tricks are available. 

This grid is extremely basic but solid. Lots of modules can be added to it flexibly without blocking each other. We'll see how this works in later sections.

2. Basic APIs
3. Modules