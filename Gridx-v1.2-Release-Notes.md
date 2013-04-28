# New Features
### HiddenColumns
     Provide APIs to hide/show columns.
### GroupHeader
     Create multi-level group header.
### PagedBody
     Add "Load More" and "Load Previous" buttons to grid body.
### Unselectable Rows
     Make some rows unselectable.
### Edit Lazy Save
     Not save to store immediately after changes are made in Edit, untial user explicitly requires so. User can undo/redo their changes before saving to store.
### HeaderRegions
     Provide a way to add extra focusable regions in column header. Basis of HeaderMenu and NestedSort.
### HeaderMenu
     Provide a way to add dropdown menus to column header.
### Adaptive Filter
     Several predefined header menus for filtering data.
### Tree grid child rows lazy loading and sorting
     Pass options parameter to getChildren function of store, so that child rows can be paged or sorted by custom logic.

# Major Improvements:
Tree module is more compatible with CellWidget module.

# Refactored Features:
Put visual index management in a separate module View.
Remove visual index related logic in Body.js and Tree.js
Refactored ColumnResizer to support GroupHeader
Refactored NestedSort to take advantage of HeaderRegions.

# Deprecated Features:
Filter api setupFilterQuery is replaced by setupQuery. The old API is still usable but deprecated.
