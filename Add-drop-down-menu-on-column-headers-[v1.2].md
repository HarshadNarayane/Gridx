The module 'HeaderMenu' provides the ability to add a menu in a column header. You can specify different menus for different column headers.
When this module is used, there's an arrow appears in the right of the header cell when mouse hover the column header, and when clicking it, a menu is shown.

The above is an sample adaptive filter implementation which is also delivered with version 1.2. Header menu is used to attach a custom filter for column 'Artist'.
You can attach any menu you want for columns.

##How to use the module 'HeaderMenu'?

####1. require 'gridx/modules/HeaderMenu', and add it the gridx definition.
For example:
```js
var grid = new GridX({
     modules: [
          'gridx/modules/HeaderMenu'
     ]
});
```

Here we don't need any configuratoin for the module. It will auto detect the menu defined in layout and auto resolve it.

####2. Define your own menus for columns
It's quite simple to attach your own menu to a column, just need define a menu property for columns in the layout.

For example:
```js
var azMenu = new AZFilterMenu({});
var numberMenu = new NumberFilterMenu({numbers: [0, 3, 6, 10]});
layout = [
{id: 'id', field: 'id', name: 'Identity', width: '80px'},
{id: 'Genre', field: 'Genre', name: 'Genre', width: '100px'},
{id: 'Artist', field: 'Artist', name: 'Artist(menu)', width: '120px', menu: azMenu},
{id: 'Track', field: 'Track', name: 'Track(menu)', width: '80px', menu: numberMenu.id},
{id: 'Year', field: 'Year', name: 'Year', width: '80px'},
{id: 'Album', field: 'Album', name: 'Album', width: '160px'},
{id: 'Name', field: 'Name', name: 'Name', width: '80px'},
{id: 'Composer', field: 'Composer', name: 'Composer', width: '160px'}
];
```

The menu could be the reference or id of the menu. The azMenu and numberMenu are sample filter menus in the folder 'gridx/support/menu'. They provide two common custom filters. See the below section.

####3. Implement your custom menu
You can use any dijit/Menu for the column, but if you want to implement grid related functions in your menu actions, there is a tricky mechanism, that is:
The header menu will try to call bindGrid(grid, col) method of the menu, if it doesn't exist, nothing will happen. With this mechanism, you have the reference to the grid and column in your menu.
For example, the provided two adaptive filters use a common base class, which implement the bindGrid method for later use.

```js
bindGrid: function(grid, col){
    //summary:
    // Attach the menu with grid, so that it could do filter actions
    this.grid = grid;
    this.colId = col.id;
}
```













