## New Features:
### ExpandableColumn
Based on GroupHeader and HiddenColumns to implement column expanding and collapsing.
### SlantedHeader
### Grid in grid
### FilterBar Conditions Persistence
### [MultiChannel] QuickFilter/Puller (experimental)
### [MultiChannel] StructureSwitch (experimental)
### [MultiChannel] Layer (experimental)
### new grid properties:
* touch
* touchModules
* desktopModules

## Major Improvements:
* Use LESS to reorganize css
* Combined and cleaned up images and/or icons
* Combined nls files into one
* Module-Set declaration

## Minor Improvements:
* Removed gridx/resources/Gridx_rtl.css and gridx/resources/claro/Gridx_rtl.css.
    ** Only one css file is needed, rlt support is already built in it.
* Sync cache is loaded by default.
    ** No need to declare cacheClass: Sync anymore.

## Refactored Features:
Pull out the common parts of PagedBody and AutoPagedBody to be a base mixin _PagedBodyMixin.

## Deprecated Features:
None.