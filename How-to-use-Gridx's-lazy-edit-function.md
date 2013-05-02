### what is lazy edit

In Gridx, user can use gridx's edit module to edit data in the cell. After changing the data in the cell and apply the changing, the data will be written back to store immediately. In Gridx 1.2, the lazy edit function is open in the edit module by default.  

Layz edit is a feature that will enable the funcionality of not write uses' changes to the store immediately. User's edit info will be held by Gridx model, and before call model.save(), user can redo and undo their changes. The edited data won't be written back to store until calling model.save() function.




Looking at the above pic, you can see those cells with a small green node at top right. That means those cells are edited and the current value you see in the grid is different from that inside the store. 

## Why using lazy edit

When using a server-side store llike dojo/store/JsonRest , the edit module without lazy edit functionality will create a single http request everytime a user edit a cell. Imaging hundreds of user editing at the same time, that would generate lots of http requsest to the server. People are caprice animals, most of the people will change multiple-times in the process of edit the data of a cell.

The essense of lazy edit is to keep user's edit info at the client side(gridx will held all the changing data) and  only write the confirmed data to the store. If the user edit the cell for 100 times only the last edit will be write back to store.

## How to use lazy edit

Using lazy edit is easy. When programmer are using the edit module in gridx, they are using lazy edit implicitly, because lazy edit feature is open by default in edit module.

var grid = new Grid({
    cacheClass: 'gridx/core/model/cache/Async',
    store: someStore,
    structure: [
        { id: 'data', field: 'data', name: 'editable data' }
    ],
    modules: [
        "gridx/modules/CellWidget",    //edit module will need cellWiget module 
        "gridx/modules/Edit",
    ]
    editLazySave: true                //not necessarily needed, because lazysave is open in edit module by default.

});
set cache class, store, structure  correctly in the gridx constructor.
include cellWidget and Edit module in the module config
set the lazySave config in the edit module to be true.(not necessarily)
By doing the above processes, if you can run gridx successfully, you can start using the lazy edit feature right now.


Key API in lazy edit

Lazy edit feature provide user with some useful API to get some edit funcionality that is easy to use. They are undo(), redo(), save(), clear(). Those APIs are not binded to the gridx.edit object. Since those funcitonality are related to data layer directly, so those APIs are binded to the gridx.model. The model is a core module in gridx for data access.

undo

When user edit some cell data and what to roll back to the last cell status. They can call the function in below way:

    var bool = grid.model.undo();    //true on undo success, otherwise false

By calling the function, the cell data throughout the grid will be roll back by 1 time. By keeping calling the undo API, user can roll back all the edits and the gridx will look like no edits have even been done on it.

redo

Opposite to undo operation, redo function will help to  re-apply the cell data edit that has been undo just now. They can call the function in below way:

    grid.model.redo();        //true on redo success, otherwise false

By calling this function, the last cell edit operation tha is undo will be restored. By keeping calling the redo API, user can restore the cell data to the current status after calling undo for some times.

clearLazyData

When user want to rollback all the edits they have done before save, they can call the function in below way:

    grid.model.clearLazyData();

By calling this funciton, all the edits will be roll back and the grid will the same as before editing. One thing need to note is that: unlike keeping calling undo() to roll back all the edits, clearLazyData will delete all the edits which means it can't be redo. So if you are sure you want to roll back to initial grid and don't want to keep those edits, you can call the clearLazyData.

save

When user finish the edit work and decide to write back those edits to the store, they can call the function in the below way:
        
    grid.model.save();
After calling this function, lazy data will be write back to store and all the edited data will ben removed.







































