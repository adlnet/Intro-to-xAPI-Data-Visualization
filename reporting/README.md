# Using xAPI to make sense of data

## Purpose
This small tutorial will teach you how to utilize the xAPI Dashboard to generate graphs to make sense of data stored in LRSs. It uses the [xAPI Dashboard](https://github.com/adlnet/xAPI-Dashboard) which relies on the [xAPI Wrapper](https://github.com/adlnet/xAPIWrapper) for LRS communication. This tutorial will show you how to:
  1.  include the xAPI Dashboard scripts in an HTML page
  2.  include the xAPI Wrapper in an HTML page
  3.  configure the xAPI Wrapper with the LRS and client credentials
  4.  retrieve statements from the LRS
  5.  create simple bar graphs
  6.  create child graphs to break the data down even further

## Step 1 - Include the xAPI Dashboard scripts in guesses.html  
The first step is to download the xAPI Dashboard files. The easiest way is to download the minified version on GitHub.  
  1.  Download the latest [xAPI Dashboard release](https://github.com/adlnet/xAPI-Dashboard/releases/tag/v1.2.2)
  2.  Save the entire folder in the reporting folder
  3.  Add a `<script>` tag in the `<head>` of `guesses.html` to include the necessary scripts.
  ``` html
  ...
    <script src="https://ajax.googleapis.com/ajax/libs/jquery/3.1.1/jquery.min.js"></script>

	<script type="text/javascript" src="xAPI-Dashboard-1.2.2/src/chart.js"></script>

	<script type="text/javascript" src="xAPI-Dashboard-1.2.2/src/dashboard.js"></script>

	<script type="text/javascript" src="xAPI-Dashboard-1.2.2/src/xapicollection.js"></script>
  ...	
  ```

## Step 2 - Include the xAPI Wrapper in guesses.html  
The second step is to download the xAPI Wrapper file. The easiest way is to download the minified version on GitHub.  
  1.  Download the latest [xapiwrapper.min.js release](https://github.com/adlnet/xAPIWrapper/releases/tag/v1.5.0)
  2.  Save `xapiwrapper.min.js` in the reporting folder with `guesses.html` (You can save it anywhere you wish, just change the source link accordingly)
  3.  Add a `<script>` tag in the `<head>` of `guesses.html` to include the xAPI Wrapper.
  ``` html
  ...
    <script src="nvd3/build/nv.d3.min.js"></script>

    <script src="./xapiwrapper.min.js"></script>
    
    <script src="https://ajax.googleapis.com/ajax/libs/jquery/3.1.1/jquery.min.js"></script>  
  ...
  ```

## Step 3 - Configure the xAPI Wrapper  
Next you have to [configure the xAPI Wrapper](https://github.com/adlnet/xAPIWrapper#xapi-launch-support). By default, the xAPI Wrapper is configured to communicate with an LRS at localhost. We want to
have xAPI Launch tell the content what configuration to use, instead of hardcoding
the LRS and authentication details. By calling ADL.launch, the xAPIWrapper will
do the handshake with xAPI Launch and pass a configured object to the callback.   
  ``` html
  ...
  <div id="endpoint"></div>
  <script>
  ADL.launch(function(err, launchdata, xAPIWrapper) {
      if (!err) {
          ADL.XAPIWrapper = xAPIWrapper;

          console.log("--- content launched via xAPI Launch ---\n", ADL.XAPIWrapper.lrs, "\n", launchdata);
      } else {
          alert("this can only be used by an xapi launch server");

          console.log("--- content not launched ---\n", ADL.XAPIWrapper.lrs);
      }
      $('#endpoint').text(ADL.XAPIWrapper.lrs.endpoint);
  }, true);
  </script>
  ...  
  ``` 

  ## Step 4 - Create a dashboard
  Next we have to initilize our dashboard. After we initilize it, we are able to provide LRS statement API [parameters](https://github.com/adlnet/xAPI-Spec/blob/master/xAPI-Communication.md#213-get-statements) to perform a query to the LRS.
    1. Create an xAPI Dashboard object just above the launch callback:
    ```javascript
    ...
    var dash = new ADL.XAPIDashboard();
    ADL.launch(function(err, launchdata, xAPIWrapper) {
        if (!err) {    
    ...
    ```

    2. xAPI Launch sends information (launch data) to the content, which the ADL.launch function sends to the callback. The
  launchdata.customData object contains content that can be configured in the
  xAPI Launch server, allowing us to enter a base URI we can use for all places
  that need a URI. Using that URI we can provide query parameters to fetch statements:
    ```javascript
    var baseURI = launchdata.customData.content;
    dash.fetchAllStatements(
            {'activity': baseURI + '/test/guess-the-number',
             'verb': baseURI + '/test/verb/ended'
            },
            function() {drawCharts();}
    );
            function() {drawCharts();}
    );
    ```
  ## Step 5 - Create our charts
  The drawCharts callback gets called automatically after we fetch our statements. This function simply clears our first graph container then calls the graphActors function which just lists everyone who has played the guess a number game and how many times they've played.
    1. Use the createBarChart function of the dash to get started:
    ```javascript
    ...
          $('#endpoint').text(ADL.XAPIWrapper.lrs.endpoint);
	  }, true);
	  
	  function drawCharts() {
	    // clears first svg container
	    d3.select('svg').empty();
	    graphActors();
	  }

    function graphActors() {
        var actors = dash.createBarChart({
        });
    }    
    ```
    2. Supply the parameters needed to display the graph and set the x and y axes. Container tells the dashboard where to display it, groupBy acts as the x axis and aggregate acts as the y axis. Always clear the charts first in case a previous chart was rendered:
    ```javascript
    function graphActors() {
        var actors = dash.createBarChart({
            container: '#chart1',
            groupBy: 'actor.account.name',
            aggregate: ADL.count(),
        });
        actors.clear();
        actors.draw();
    }
    ```

    3. Now that we have our first graph, wouldn't it be nice to drill down into it a bit more after we click a bar? Let's add a child graph to it that displays all of the times our selected user played the game and how many guesses per game they had:
    ```javascript
    function graphActors() {
        var actors = dash.createBarChart({
            container: '#chart1',
            groupBy: 'actor.account.name',
            aggregate: ADL.count(),
            child: actorChart
        });
        actors.clear();
        actors.draw();
    }

    var actorChart = dash.createBarChart({
        container: "#chart2",
        groupBy: 'timestamp',
        aggregate: ADL.count(),
    });
    ```

    4. Notice all we can see is the first graph and nothing is appearing on the child graph yet. We need to process some data first to display what we want. The process parameter takes a function to process the data before using whatever aggregate function is supplied.
    ```javascript
    ...
        groupBy: 'timestamp',
        process: function (data, event, opts) {
            // the map/filter functions make sure no funny statements were collected
            // and ensure all actors have an account and accound name. The event.in
            // data is the name of the actor clicked. We only want to return his data
            // and only if it's not undefined
            var d = data.contents.map(function(cur, idx, arr) {
                if (cur.actor.account && cur.actor.account.name == event.in) 
                   return cur;
            }).filter(function(n){ return n != undefined });
            // opts callback is always needed with process. in value is the label for 
            // the out value (y-axis)
            opts.cb( d.map(function(node) {
                return {
                    in: node.timestamp, 
                    out: node.result.extensions[baseURI + '/guess-the-number/ext/guesses'].length, 
                    stmt: node
                };
            }) );
        },
        aggregate: ADL.count(),    
    ...
    ```

    5. We can also customize the labels on the charts. Let's do a little customization and even add another child chart for even further insight:
    ```javascript
    ...
        },
        aggregate: ADL.count(),
        customize: function (chart) {
            chart.xAxis.rotateLabels(45);
            chart.xAxis.tickFormat(function(d){
                return new Date(d).toLocaleString()});
        },
        click: function(e){
            guesses(e);
        } 
    ```

    6. Now that we've used the xAPI Dashboard to create a few charts, let's try making one directrly with the d3 library. Create the guesses function to show a line graph of the actual guesses the player had:
    ```javascript
    ...
            guesses(e);
        }
    });
    var guesses = (function(event) {
    	// call the nv library and style it
        var chart = nv.models.lineChart()
            .useInteractiveGuideline(true)
            .x(function(d) { return d[0] })
            .y(function(d) { return d[1] })
            .color(d3.scale.category10().range())
            .duration(300)
            .clipVoronoi(false)
            .interpolate("cardinal")
        ;
        // call d3 to populate chart with data
        d3.select('#chart3')
            .datum(formatData(event.data))
            .call(chart);
        nv.utils.windowResize(chart.update);

        return chart;
    });    
    ```

    7. Almost there...we have to format the data for some rhyme or reason so let's write that function:
    ```javascript
    ...
    function formatData(data) {
        var ret = [{key: "guesses", values: []},{key: "answer", values: []}],
            gs = data.stmt.result.extensions[baseURI + '/guess-the-number/ext/guesses'],
            ans = data.stmt.result.extensions[baseURI + '/guess-the-number/ext/number'];
        
        for (var i = 0; i < gs.length; i++) {
            ret[0].values.push([i, gs[i]]);
            ret[1].values.push([i, ans]);
        }
        return ret;
    }    
    ```