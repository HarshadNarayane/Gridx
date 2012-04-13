This article explains the design of Gridx Model in the following aspects: Why? What? How?

# Why Gridx Model?

A Grid usually provides a lot of UI tools to manipulate data, such as filter, sorting, selection, drag & drop, paging, editing, etc. Different UI tools can have different look and feels and even completely different UI design, but their core functionality on data manipulation might be the same. For example, filter is a very useful UI tool of grid, but different requirements will lead to different UI design. A powerful and therefore complicated design might allow complicated filter conditions including logic AND/OR/NOT operators, and automatically recognize data types other than string. However, what if a simple string filter box would just be sufficient? It'll be wonderful if we have a separated filter logic without UI, and a bunch of filter UIs relying on this logic. This idea is called MVC (Model, View, Controller) pattern.

Gridx Model is designed to be the "Model" of grid, so that all the grid UI tools can focus on their UI features without worrying about the underlying data manipulation logic. But those who are familiar with dojo data store will probably ask: why not directly use dojo stores as the model? This is really a good question. And here're some reasons.

### 1. The concept of row index is not complete in dojo store. 
For example, you can't ask store for a row's index when you have its ID. Row indexes are only meaningful in the query method (similar to the fetch method for old dojo.data stores) of store. But it's quite common to deal with row indexes in a grid. Users tend to refer to a row by "the first row", "the second row", etc, instead of row "A", row "B", because row IDs are usually invisible on grid. But since grid can be sorted, filtered, paged and even reordered, row indexes are not as consistent as row IDs. For example, when you select the first row, you want to select the first row itself, so that when the grid is sorted, the selection UI goes with the previous first row. In this process, row index 0 needs to be transformed to row ID during selection and then the row ID needs to be transformed back to row index during sorting (remember DataGrid?). It would be very convenient if the store provide such APIs for transforming between row IDs and row indexes. But they don't, so we create our own.

### 2. The store does not provide a useful cache mechanism. 
Unlike other dijits, grid tends to query the store quite often, especially when using lazy loading and virtual scrolling. In most cases, it'll be annoying if the grid needs to fetch the store again when you scroll it to a previously displayed position. It might be extremely slow for a server side store. So every grid that supports "virtual scrolling" needs some sort of caching. But grid cache is not only for faster rendering. Users can access a grid row at any time, even when the row is not yet displayed. Wouldn't it be wonderful if the previously accessed (not only rendered) rows be cached? Think about the export feature of grid, why need to query the store again when all the rows are already cached in client side? So what we need here is a formal cache mechanism that will benefit  every feature in grid as long as it needs to access row data. Store does not have such cache (dojo.store.Cache is a different story), so we create our own.

### 3. The difference between synchronous stores and asynchronous stores needs to be hidden.
Client side stores are usually synchronous, and server side stores are usually asynchronous. The existence of async logic makes a lot of things complicated. For sync stores, all the rows are actually at client side, so a dynamic cache is meaningless. A lot of code will be saved for a sync store because we won't need so many callback functions. But this can not happen if the grid has to treat sync stores and async stores equally. The store API set contains many async methods, which means every call to these methods will need at least one callback function. What if we have only one async method? Then most code can be shared among sync and async logic, and async code can be minimized. Is this possible? Anyway, we tried it in Gridx.

### 4. The grid data is not necessarily the store data.
Dojo store is a general purpose data source, and grid is just a "window" to show a certain aspect of the data. This not only means the data in store is not necessarily displayed in grid, but also implies that a grid can define its only way to show the data. In old DataGrid and EnhancedGrid, we have the "formatter" function and "get" function, which can change the store data before it is shown in grid. Now for Gridx, we have similar things, and their functionalities are more clearly defined and better supported. Detailed descriptions on this will explained in later articles. By now the important thing is to understand that grid does not only show data, it also has to define what data to show, and how to show it. If we don't have a separate layer like Gridx Model, we'll have to explicitly deal with the difference between the data in store and the data shown in grid all the time.

There are also other reasons that a Grid Model is necessary. For example, selection and reordering are not supported in store; the filter feature in store (seems the dojo.store.Memory has enhanced this now, but others still haven't) is not powerful enough; And the new store API (dojo.store) was not even stable yet when Gridx was developed. Anyway, a solid base that can be completely controlled by ourselves is very important. If someday the store changes again, don't worry, we only have one place to change: the Gridx Model.

# What's Gridx Model?

The Gridx Model is defined by the following set of APIs:

	1. when()
	2. byIndex()
	3. byId()
	4. indexToId()
	5. idToIndex()
	6. size()
	7. treePath()
	8. hasChildren()

These APIs are stable, which means their signatures and basic functionalities should never be changed. The most important API here is the "when" method. This is the only async method in Gridx Model. It's job is to ensure that all the required rows are already at client side when the callback function is called. All other methods are sync, so it makes no difference between client side store and server side store. So the grid user can first write a bunch of code for client side store, and then just wrap the whole code snippet by model.when({/* required rows */}, function(){...}), it'll work for a server side store. (Eh...things get a little more complicated when Model-modifying operations are included. See the next section.)

The Gridx Model itself can also be extended, so that extra APIs can be added. For now, the following extended (and unstable) APIs are available:
<pre>
// Available in Model itself
scan()
// Available in Async cache
keep() 
free()
// Available in ClientFilter extension
filter()
hasFilter()
// Available in Mark extension
markById()
markByIndex()
clearMark()
isMarked()
getMarkedIds()
// Available in Move extension
move()
moveIndexes()
insert()
// Available in Query extension
query()
// Available in Sort extension
sort()
</pre>
Details of these APIs can be found in API docs.
The Gridx Model is designed to be extensible through "Extension"s. Extensions can be thought of as a serials of wrappers, which wrap the store layer by layer. The inner-most wrapper is called "Cache", which should provide all the stable APIs of Gridx Model and implement a cache mechanism if needed. This "Cache" is always required, maybe you can think of it as a "core extension". Cache distinguishes sync store and async store. For async cache, a lazy-loading logic is implemented, including a finite cache mechanism. For sync cache, it simply caches everything in the store. Grid users need to pick which cache implementation to use when creating grid. All other extensions are optional. If they are not needed, their code won't be loaded.

The Model Extensions are so "core" and "basic" that they are not meant to be handled or even noticed directly by grid users (cache is an exception). Actually they are for grid developers. When grid developers are writing a grid module, he should include proper Model Extensions for that module. For example, a select module which provides some services to select grid rows should include the "Mark" extension, while a sort module should include the "Sort" extension.
Details on how to include Model Extensions in a module will be covered when we talk about modules.

# How to use Gridx Model?

Actually, Gridx Model can be used alone, without any UI. A Gridx Model instance is a logical grid itself. The fact that Gridx Model and all other parts of grid are completely separated makes it possible (at least theoretically) to write your own grid based on Gridx Model, or to implement your own Gridx Model for the existing grid UI.
If you have a Gridx instance: grid, for example, then grid.model will be the Gridx Model for this grid. Note that grid.model can not be shared among grids. And it is also readonly.

Gridx Model APIs can be categorized into 3 classes: queries, commands, and "when". The only rule is, call "when" before queries. For instance:
<pre>
// Do commands first
model.sort([{colId: 'column1'}]);
model.filter(function(data){ return data.column1 % 2; });
model.move(100, 4, 10);

// "When" all the required rows are ready....
model.when({start: 0}, function(){
    // anything possible to use these rows...
    var row1 = model.byIndex(0);
    var row2 = model.byId('abc');
    console.log(row1.data.column1);
    console.log(row2.rawData.field1);
});
</pre>
So the pattern is: 
1. Do some commands to change to grid model;
2. Execute all the commands and prepare needed rows;
3. Use the rows

This pattern is quite common if you want to do anything interesting with grid in programmatic way. The key point here is: all these commands return immediately (synchronous). Their actual execution is delayed to the next "when". In this way, you can directly put all the commands together without worrying about their execution order. For example, the "move" command in the above example is guaranteed to execute after the model is sorted and filtered.

Another benefit is: the "when" method is the only asynchronous method that needs a callback. So the difference of handling client store and server side store is minimized. Think about the case if every command was asynchronous when using server side store:
<pre>
// Assuming all these methods were asynchronous, then we have to write...
model.sort([{colId: 'column1'}]).then(function(){
    model.filter(function(data){return data.column1 % 2; }).then(function(){
        model.move(100, 4, 10).then(function(){
                var row1 = model.byIndex(0);
                var row2 = model.byId('abc');
                console.log(row1.data.column1);
                console.log(row2.rawData.field1);
                //........
        });
    });
});
</pre>
This is not only harder to write but also slower to run because every command has to start a new http request.

Actually the dojo store (both the new dojo.store and the old dojo.data) prefers this pattern. The "query" (or "fetch" for dojo.data) method is similar to the "when" function because you have to prepare the sorting order and the query string before you execute the query:
<pre>
// Place some commands...
var sort = [{attribute: 'field1'}];
var query = {field1: 'a*'};
// Then execute them and prepare rows....
store.query(query, {sort: sort, start: 0}).then(function(items){
    // Then use the rows
});
</pre>
Of course the store can not "remember" the executed commands, and some other commands does not have proper corresponding arguments (like complicated filter and row moving). That's why we need our own model.

