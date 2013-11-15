## New Features:
#### ExpandableColumn
Based on GroupHeader and HiddenColumns to implement column expanding and collapsing.
#### SlantedHeader
Make header slanted to better display small cell content with long header title. Compatible with ExpandableColumn.
#### Grid in grid
Allow putting another Gridx inside Details on Demand (Dod).
#### FilterBar Conditions Persistence
Allow persisting advanced filter conditions. Persistence name is "filterbar".
#### [MultiChannel] Puller (for QuickFilter, experimental)
When used on mobile device, QuickFilter bar can be pulled out (or pushed into) grid header.
#### [MultiChannel] StructureSwitch (experimental)
Provided an easy why to specify which column to use based on device orientation or customized conditions.
#### [MultiChannel] Layer (experimental)
Mobile version of tree grid. Only show one level of rows at a time.
#### new grid properties:
* touch: true if grid is on a touch device, false otherwise. Also can be manually set when creating grid.
* touchModules and desktopModules: only `touchModules` will be loaded when `grid.touch` is true; only `desktopModules` will be loaded when `grid.touch` is false. Note that `modules will always be loaded.

## Major Improvements:
* Use LESS to reorganize css
* Combined and cleaned up images and/or icons
* Combined nls files into one
* Module-Set declaration

## Minor Improvements:
* Removed gridx/resources/Gridx_rtl.css and gridx/resources/claro/Gridx_rtl.css. Only one css file is needed, rlt support is already built in it.
* Sync cache is loaded by default. No need to declare `cacheClass:Sync` anymore.

## Refactored Features:
Pull out the common parts of PagedBody and AutoPagedBody to be a base mixin _PagedBodyMixin.

## Deprecated Features:
None.