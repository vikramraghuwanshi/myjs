myjs
====
<html>
  <head>
    <script type="text/javascript" src="https://www.google.com/jsapi"></script>
    <script type="text/javascript" src="http://ajax.googleapis.com/ajax/libs/jquery/1.8.2/jquery.min.js"></script>
  </head>
  <body>
    
  
<div id="colFilter_div"></div>
<div id="colFilter_div1"></div>
<div id="chart_div"></div>
<div id="creativeCommons" style="text-align: center; width: 400px;">
</div>
<script>
  google.load('visualization', '1', {packages: ['controls']});
google.setOnLoadCallback(drawChart);

function drawChart () {
    var data = new google.visualization.DataTable();
   data.addColumn('string', 'Gender');
    data.addColumn('number', 'Male');
    data.addColumn('number', 'Female');    
    data.addRows([
        ['Dog',  45, 60],
        ['Cat',  155, 50],
        ['Cow',  35, 31],
        ['Bird',  105, 23],
        ['camel',  120, 56],
        ['Giraf',  65, 19],
        ['Han',  80, 23]       
    ]);
    
    

    var columnsTable = new google.visualization.DataTable();
    columnsTable.addColumn('number', 'colIndex');
    columnsTable.addColumn('string', 'colLabel');
    
    var columnsTable1 = new google.visualization.DataTable();
    columnsTable1.addColumn('number', 'colIndex');
    columnsTable1.addColumn('string', 'colLabel');
    
    var initState= {selectedValues: []};
    var initState1 = {selectedValues: []};
    // put the columns into this data table (skip column 0)
    for (var i = 1; i < data.getNumberOfColumns(); i++) {
        columnsTable.addRow([i, data.getColumnLabel(i)]);
        // you can comment out this next line if you want to have a default selection other than the whole list
        initState.selectedValues.push(data.getColumnLabel(i));
    }
    
    //This is for numberof rows........
    var rowInds = data.getSortedRows([{column: 1}]);
    for (var i = 0; i < data.getNumberOfRows(); i++) {
      //console.log(data.getValue(rowInds[i],0));
      initState1.selectedValues.push(data.getValue(rowInds[i],0));
    }
    //console.log(initState1);
    // you can set individual columns to be the default columns (instead of populating via the loop above) like this:
    // initState.selectedValues.push(data.getColumnLabel(4));
    
    var chart = new google.visualization.ChartWrapper({
        chartType: 'ColumnChart',
        containerId: 'chart_div',
        dataTable: data,
        options: {
            colors: ['red','yellow'],
            title: 'Company Performance',
            width: 600,
            height: 400
        }
    });
    
    var columnFilter = new google.visualization.ControlWrapper({
        controlType: 'CategoryFilter',
        containerId: 'colFilter_div',
        dataTable: columnsTable,
        options: {
            filterColumnLabel: 'colLabel',            
            ui: {
                label: 'Gender',
                allowTyping: false,
                allowMultiple: true,
                allowNone: true,
                selectedValuesLayout: 'belowStacked',
                
            }
        },
       //state: {}
    });
    
    var columnFilter1 = new google.visualization.ControlWrapper({
        controlType: 'CategoryFilter',
        containerId: 'colFilter_div1',
        dataTable: data,
        options: {
            filterColumnIndex: 0,
            ui: {
                label: 'Animal',
                allowTyping: false,
                allowMultiple: true,
                allowNone: true,
                selectedValuesLayout: 'belowStacked',
                
            }
        },
        //state: {}
    });
    
    function setChartView () {
        var state = columnFilter.getState(),
        state1 = columnFilter1.getState();
        var row;
        var view = {
            columns: [0],
            rows:[0]
        };      
        
        
        if (typeof state.selectedValues != 'undefined' && state.selectedValues.length>0) {
          for (var i = 0; i < state.selectedValues.length; i++) {
            row = columnsTable.getFilteredRows([{column: 1, value: state.selectedValues[i]}])[0];
            view.columns.push(columnsTable.getValue(row, 0));
          }          
        }
        else {
            for (var i = 1; i < data.getNumberOfColumns(); i++) {
              view.columns.push(i);
          }
        }
        
        
        
        if (typeof state1.selectedValues !='undefined' && state1.selectedValues.length>0) {
            for(var j=0;j<state1.selectedValues.length;j++){
              row = data.getFilteredRows([{column: 0, value: state1.selectedValues[j]}])[0];
              view.rows[j]= row;  
            }
        }
        else {
          var rowInds = data.getSortedRows([{column: 1}]);       
          view.rows = rowInds;
        }
        
        //for (var i = 0; i < state.selectedValues.length; i++) {
        //    row = columnsTable.getFilteredRows([{column: 1, value: state.selectedValues[i]}])[0];
        //     console.log("Row="+i+"="+row+" Name="+columnsTable.getValue(row, 0));
        //    view.columns.push(columnsTable.getValue(row, 0));
        //}
      
        //for(var j=0;j<state1.selectedValues.length;j++){
        //  row = data.getFilteredRows([{column: 0, value: state1.selectedValues[j]}])[0];
        //  view.rows[j]= row;         
        //}
        var colors=["#3366cc","#dc3912"];
       
        
        // sort the indices into their original order
        view.columns.sort(function (a, b) {
            return (a - b);
        });
       
       
      chart.getOptions().series=[];
      for(var i=1;i<view.columns.length;i++){
          chart.getOptions().series.push({color:colors[view.columns[i]-1]});
      }
        
        chart.setView(view);
        chart.draw();
    }
    google.visualization.events.addListener(columnFilter, 'statechange', setChartView);
    google.visualization.events.addListener(columnFilter1, 'statechange', setChartView);
    
    setChartView();
    columnFilter.draw();
    columnFilter1.draw();
}

</script>
</body>
</html>
