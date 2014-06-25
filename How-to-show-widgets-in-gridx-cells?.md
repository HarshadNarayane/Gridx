In gridx column definition, a **decorator** function can be provided to show any HTML/CSS in cell. But sometimes this is not enough. When complicated widgets need to be put into cells, the pure string returned from **decorator** can no longer do the job.  So the **gridx/modules/CellWidget** module shows its value.

![gridx CellWidget](http://oria.github.io/gridx/tutor/image/gridx-12.png)

## Why CellWidget
The CellWidget module attaches widgets to cell nodes by means of the **onAfterRow** event of the grid body. The key idea is to do this job in an efficient way. 

Grid body gets refreshed from time to time, and the cause can be sorting, filtering, paging, virtual scrolling, setting value, expanding tree node, etc, etc. Everytime the body is refreshed, the rows in it are re-constructed, and the **onAfterRow** event will be fired. If we always create new widgets in **onAfterRow**, it would be quite wasteful: tens of widgets would be destroyed immediately after they are created if you just click the header twice in order to make a column descendingly sorted. So some "cache" mechanism must be established. The first idea is to use exactly one widget for every cell, no matter how many times it gets refreshed. This solves some of the refresh problems, but it is still inefficient if we have a lot of rows, say, 100000. Just think about if a user gradually scrolls this huge grid to view every row, how many widgets will have to be created eventually!

So the goal of the CellWidget module is to create as few widgets as possible. The way to accomplish this is to reuse them among different rows through `set('value', ...)`. 

## How to use CellWidget
Setting value to a widget is usually much faster than creating a new one. This is good idea, but it also requires the user to be aware of this "reusing" thing. Let's see how to use the CellWidget module first:

```js
var grid = new Grid({
  cacheClass: 'gridx/core/model/cache/Async',
  store: someStore,
  structure: [
    { id: 'progress', field: 'progress', name: 'Install Progress',
      widgetsInCell: true,
      decorator: function(){
        return "<div data-dojo-type='dijit.ProgressBar' data-dojo-props='maximum: 1' " + 
            "class='gridxHasGridCellValue' style='width: 100%;'></div>;";
      }
    }
  ],
  modules: [
    "gridx/modules/CellWidget"
  ]
});

```

* First, don't forget to require all the necessary resources: the store, the cache, the modules, and the widgets you want to show in the cells. If you still have trouble in creating a gridx, please take a look at [this tutorial](https://github.com/oria/gridx/wiki/Create-the-Simplest-Gridx).<br/>
* Second, set **widgetsInCell** to true in the columns that you want to show widgets. The CellWidget module will only be effective on these columns.<br/>
* Third, provide the **decorator** function for your **widgetsInCell** columns.

### Return template string from decorator
Here comes the first trick: the **decorator** function here does not have any arguments. This is not the case when **widgetsInCell** is false. Usually the current cell data will be passed, as well as the curent row ID and index. Then why? Because this **decorator** function is now returning a template string, from which a "cell widget" will be created and reused among different rows. If some specific cell data is passed in, how can it be reused then? So this template string must not contain any specific row information. In the above example, a dijit/ProgressBar is put in the template, its arguments are embedded in data-dojo-props. Note the **gridxHasGridCellValue** class, this is the second trick on how widgets get reused. The CellWidget module will automatically find all the widgets with this special class when rendering every row, and call `set('value')` to set the proper cell data into it.

### Setup widgets in setCellValue
In the above example, things are simple because no data converting is needed before `set('value')` to the widget. What if you'd like to do something interesting when setting widgets value? The **setCellValue** function comes to rescue:

```js
{ id: 'progress', field: 'progress', name: 'Install Progress',
  widgetsInCell: true,
  decorator: function(){
    return "<div data-dojo-type='dijit.ProgressBar' data-dojo-props='maximum: 1' " +
      "data-dojo-attach-point='progBar' style='width: 100%;'></div>";
  },
  setCellValue: function(gridData, storeData, cellWidget){
    var data = doSomethingIntersting(gridData);
    cellWidget.progBar.set('value', data);
    // cellWidget.cell give you full access to everything you want.
    var rowIndex = cellWidget.cell.row.index();
  }
}
```

The **setCellValue** function will get called every time a row is rendered. When it is called, the widget is already created and you have full control of it. You can set values to the widgets, modify css, or even manipulate the dom nodes and connecting events. The third parameter "cellWidget" in this function refers to the "cell widget" itself, it is the widget that owns the template string returned from the "decorator" function, so you can access any "dojo attach point" defined there. The first and second parameters are grid data and store data for this current cell respectively. They are only different when the "formatter" function is provided. You can also get the current cell from `cellWidget.cell`, from which you can literally get everything you need.
<a id="events"></a>

<a name='events'></a>
### How to handle widget events
But remember that the widgets ("cell widget" as a whole) are reused among different rows. So if you bind some events or changed some dom nodes in the **setCellValue** function, these changes will be retained for another row to be reused. If you just keep "connecting" events without "disconnecting", there will be huge memory leak. So the proper way is to save the connections and disconnect them in setCellValue. For example

```js
{ id: 'progress', field: 'progress', name: 'Install Progress',
  widgetsInCell: true,
  decorator: function(){
    return "<button data-dojo-type='dijit.form.Button' data-dojo-attach-point='btn'></button>";
  },
  setCellValue: function(gridData, storeData, cellWidget){
    cellWidget.btn.set('label', gridData);
    if(cellWidget.btn._cnnt){
      // Remove previously connected events to avoid memory leak.
      cellWidget.btn._cnnt.remove();
    }
    cellWidget.btn._cnnt = dojo.connect(cellWidget.btn, 'onClick', function(e){
      alert(gridData);
      // do your job here......
    });
  }
}
```

This seems not very straightforward, I admit. So in gridx 1.2 a new method (callback) named getCellWidgetConnects is introduced to make this job simpler:

```js
{ id: 'progress', field: 'progress', name: 'Install Progress',
  widgetsInCell: true,
  decorator: function(){
    return "<button data-dojo-type='dijit.form.Button' data-dojo-attach-point='btn'></button>";
  },
  setCellValue: function(gridData, storeData, cellWidget){
    cellWidget.btn.set('label', gridData);
  },
  getCellWidgetConnects: function(cellWidget, cell){
    // return an array of connection arguments
    return [
      [cellWidget.btn, 'onClick', function(e){
        alert(cell.data());
        // do your job here.....
      }]
    ];
  }
}
```

With this **getCellWidgetConnects**, gridx can manage the connections for you. Connecting and disconnecting are done automatically.

### Other advanced callbacks
Besides this, another 2 methods are available since gridx 1.2 to provide more meaningful names on the job you are doing:

```js
{ id: 'progress', field: 'progress', name: 'Install Progress',
  widgetsInCell: true,
  decorator: function(){
    return "<button data-dojo-type='dijit.form.Button' data-dojo-attach-point='btn'></button>";
  },
  setCellValue: function(gridData, storeData, cellWidget){
    cellWidget.btn.set('label', gridData);
  },
  getCellWidgetConnects: function(cellWidget, cell){
    // return an array of connection arguments
    return [
      [cellWidget.btn, 'onClick', function(e){
        alert(cell.data());
        // do your job here.....
      }]
    ];
  },
  initializeCellWidget: function(cellWidget, cell){
    // create extra widgets or manipulate dom nodes that depends on current cell context.
    cellWidget.anotherButton = new Button({...});
    cellWidget.domNode.append(cellWidget.anotherButton.domNode);
  },
  uninitializeCellWidget: function(cellWidget, cell){
    // don't forget to undo the changes you made in initializeCellWidget, so that it can be reused among different rows.
    cellWidget.anotherButton.destroy();
  }
}
```

As you can see that actually you can do all these stuff in the setCellvalue function. This new approach just provides more semantics, makes your code easier to read and save you some extra comments.

### Programmatic way to create widgets
If you've ever used DataGrid, you might be familiar with returning widget in the **formatter** function, and feeling uncomfortable to write template strings.  Then you can use the **onCellWidgetCreated** event and even omit the "decorator" function (since gridx 1.2):

```js
{ id: 'progress', field: 'progress', name: 'Install Progress',
  widgetsInCell: true,
  onCellWidgetCreated: function(cellWidget, column){
    var btn = new Button({...});
    btn.placeAt(cellWidget.domNode);
  }
}
```

This **onCellWidgetCreated** is only fired when a new cell widget is created. It won't be fired when widget is reused. So same as the decorator function, no row specific information should be used here.

## Conclusion
Here's a conclusion on how to show widgets in gridx cells:
 1. require and declare **gridx/modules/CellWidget** for your grid.
 2. set **widgetsInCell** to true for your columns.
 3. return template string in **decorator** function, or create some in **onCellWidgetCreated** event handler, or both.
 4. set widget values and status in **setCellValue** function (optional)
 5. provide event connections in **getCellWidgetConnects** (optional, since 1.2)
 6. do extra manipulation in **initialCellWidget** and **uninitialCellWidget** (optional, since 1.2)