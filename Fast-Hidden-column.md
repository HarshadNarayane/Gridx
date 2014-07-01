If you want hidden a column set width to 0px in structure

ex:

Structur = [
        { field:'id', name:'Id',width:'0px'},
        { field:'nome', name:'Nome',width:'200px'},......

!IMPORTANT

In Firefox >= 8 you must add this code in your css

/* firefox td width 0px*/

.gridxCell[style*="width: 0px;"]{
    margin:0px;
    padding:0px;
    border:0px;

}