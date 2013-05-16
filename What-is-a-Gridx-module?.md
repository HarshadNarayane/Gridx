Strictly speaking, a Gridx module is just a JavaScript class that defines a `name` property. This `name` property represents the "interface" of this module, or you can also regard it as a feature name. Two different modules can have the same name, meaning they implement the same feature in two different ways. But for each name, there is at most one effective module in a running gridx instance.

But usually a Gridx module inherits from gridx/core/_Module.js, which defines a bunch of useful methods for module developers, such as `connect()`, `subscribe()`, `arg()`, etc.

### Module Life-cycle
A Gridx module can have some optional life-cycle functions: `constructor()`, `preload()`, and `load()`. The creating process of gridx is just a process of creating all the modules, one by one, and "load" them according to their denpendancy order.

<pre>
Gridx creation steps:
1. create the "model"
2. "new" all modules
3. "preload" all modules
4. "load" all modules
</pre>

#### constructor()
This is called when a module is "newed". At this time, the module knows nothing about other modules (maybe they haven't been "newed" yet). Property initializations that is determined by this module itself can be done here.

#### preload()
If this function exists, it is called after all modules are "newed". Now the module knows that other module instances already exist, so it can call their method as long as it knows what it's doing.

#### load()
If this function exists, it is called after all modules are "preloaded", AND all the dependent modules of this module have finished "loading". In this function we can use more features that other modules provide, because they are ensured to be available now.

### Module dependency
A module can depend on other modules, so that code can be more effectively reused. But this "dependency" should not be on the actual implementation, otherwise the "depended" modules would not be able to be replaced by new implementations.
Gridx accomplishes this by "name dependency": a module only depends on the `name` of other modules. For example, PaginationBar module depends on Pagination module, which provides some useful paging functions. In the code of PaginationBar, the Pagination module is not even required, it just declares that it depends on any module that has a `name` of "pagination". So users can replace the default Pagination implementation with anything they like, as long as it provides similar API to support PaginationBar.
And by this way, users can even replace any "core modules" (loaded-by-default modules). One example is the VirtualVScroller module, it is widely used to replace the default VScroller module, to provide "virtual scrolling" feature.

Every module can the following 3 optional properties to declare its dependancy: `forced`, `required`, and `optional`.
* `forced: ["header", "body"]` // means this module won't be loaded until "header" module and "body" module have finished loading.
* `required: ["tree"]` // means this module requires the existance of "tree" module, but it can be loaded at any time.
* `optional: ["edit"]` // means if some "edit" module exists, it must be loaded before this module.

The Gridx core uses these 3 properties to automatically determine the initialization order of all modules.

### Module parameters
Almost everything in Gridx is built by some module, so how to configure the modules is a very important question.
Passing parameters to modules can be done when creating Gridx by means of "grid parameters". For example, a gridx instance with extendedSelect/Row module can be created by:

<pre>
var grid = new Grid({
     cacheClass: Cache,
     store: store,
     structure: [...],
     modules: [
          "gridx/modules/extendedSelect/Row"
     ],
     <b>selectRowTriggerOnCell: true,
     bodyRowHoverEffect: false</b>
});
</pre>

Here we set the `triggerOnCell` property of the extendedSelect/Row module to be true. This is done by passing `selectRowTriggerOnCell: true` to grid. Note the pattern here is: 

<pre>
moduleName + moduleParameterName (camel format).
</pre>

Using this way you can also passing parameters to core modules that not explicitly declared in the modules array. In the above example it is the "rowHoverEffect" parameter of the "body" module: `bodyRowHoverEffect: false`.

Actually, module parameters can also be passed in the following way:

<pre>
var grid = new Grid({
     cacheClass: Cache,
     store: store,
     structure: [...],
     modules: [
          {
               moduleClass: "gridx/modules/extendedSelect/Row",
               triggerOnCell: true
          },
          {
               moduleClass: "gridx/modules/Body",
               rowHoverEffect: false
          }
     ]
});
</pre>

But this needs more typing and the code structure is more complicated. So I prefer the first way. :-)

### Sample Module
There's a "sample module" under gridx/modules/ directory, called "SampleModule.js.txt", in which more details on how to create a module is explained. You can start writing your own module based on that file.

Enjoy!