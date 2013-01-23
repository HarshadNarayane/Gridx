The current gridx UI is designed for desktop. All the row hover effect, sorting effect, row/column moving, column resizing, and a lot of keyboard hanlding, makes gridx not suitable for mobild devides right now. But in some cases, we need our page to be operatable in mobile devices. No need to be fancy on gestures, just working is fine.

So here comes the `TouchScroll` module (`gridx/modules/TouchScroll`). When this module is added, the grid will be scrollable on touch devices like iPad. But just scrollable, no bouncing effect, no sliding effect yet:

<pre>
var grid = new Grid({
     ......
     modules: [
          "gridx/modules/TouchScroll"
     ]
});
</pre>

Note that it is also compatible with the virtual scroll module `VirtualVScroller`:

var grid = new Grid({
     ......
     modules: [
          "gridx/modules/TouchScroll",
          "gridx/modules/VirtualVScroller"
     ]
});

The idea of this module is to use the moving distance during touch move gesture to set the `scrollTop`/`scrollLeft` of the scroll bar.

This module will have no effect when showing on desktop. So it's safe to add it if your page will probably be shown on touch devices.

If you do need the bouncing/sliding effect during touch scrolling, you can try `gridx/modules/TouchVScroller`. Unlike the `TouchScroll` module, this one is a complete implementation of the vertical scroller of gridx. So it can NOT work together with `VirtualVScroller`. That means all rows will be rendered out when grid is first shown (no lazy rendering).

<pre>
var grid = new Grid({
     ......
     modules: [
          "gridx/modules/TouchVScroller"     // although the name is similar, it is totally different from TouchScroll!
     ]
});
</pre>

`TouchVScroller` uses `dojox/mobile/scrolllable` to implement the bouncing and sliding effects. It is still experimental now and have some known issues, like sometimes header and cell not aligned during scrolling.
