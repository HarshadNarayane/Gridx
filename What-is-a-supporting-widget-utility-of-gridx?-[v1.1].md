A gridx supporting widget or utility is anything that works for a gridx. It is not a part of gridx, even if it has some UI. 

In Gridx 1.1, supporting widgets and utilities are moved out of the gridx/modules/ directory and placed in a separate one: gridx/support/. Currently there are 7 supporting widgets and 3 supporting functions:

<table>
<tr><th>Supporter</th><th>Type</th><th>Sample</th></tr>
<tr><td>LinkPager</td><td>Widget</td><td></td></tr>
<tr><td>LinkSizer</td><td>Widget</td><td></td></tr>
<tr><td>DropDownPager</td><td>Widget</td><td></td></tr>
<tr><td>DropDownSizer</td><td>Widget</td><td></td></tr>
<tr><td>GotoPageButton</td><td>Widget</td><td></td></tr>
<tr><td>Summary</td><td>Widget</td><td></td></tr>
<tr><td>QuickFilter</td><td>Widget</td><td></td></tr>
<tr><td>exporter/toCSV</td><td>Function</td><td></td></tr>
<tr><td>exporter/toTable</td><td>Function</td><td></td></tr>
<tr><td>printer</td><td>Function</td><td></td></tr>
</table>

Note that exporter and printer are now just functions, accepting a gridx instance as their first argument:

<pre>
require([
     ......
     "gridx/support/exporter/toCSV"
], function(......, toCSV){
     toCSV(grid, {...});
});
</pre>

Although they can be used in the Bar module, as described in this artical, they can also be used separately. For example, you can create a `gridx/support/LinkPager` widget in another area of your page far from the grid it is working for. All you have to do when creating them, is to pass a grid instance:

<pre>
var pager = new LinkPager({
     grid: grid     // a grid instance is expected
});
pager.placeAt("someDomNodeFarFromGridx");
pager.startup();
</pre>

Besides this, it is just like any other ordinary widget. Nothing special.<br/>

Yes, you can also write one for yourself, or contribute back here. :-)