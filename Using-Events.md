Many of the modules provided by GridX offer callback events.  These events can be connected using the module's provided `connect(obj, event, function, context)` method.

For example, if we have included module type XYZ, then we will find that the grid object will have a child object also called XYZ.  To add an event handler, code:

<pre>
myGrid.XYZ.connect(myGrid.XYZ, "onSomeEvent", function(...) {...});
</pre>