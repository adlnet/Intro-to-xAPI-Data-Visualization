# Hands On Reporting
Welcome to the hands on portion of the session. The following are some challenges to highlight ADL's Dashboard and Collection libraries, while giving you exposure to working with statements. The xAPI Dashboard is included in the GitHub project for your use, or download the dashboard files [here](https://github.com/adlnet/xAPI-Dashboard/releases/tag/v1.2.1).  
  
The following is a template you can use for the bar charts. Please note that the `<script>` elements may need to be updated to point to the location of the Dashboard files (nv.d2.css, xapidashboard.min.js & xapicollection.min.js). Optionally, you may use this [codepen example](http://codepen.io/anon/pen/XbqyrN) as a base temple, or example.  
  
```html
<!doctype html>
<html lang="en">
    <head>
        <meta charset="utf-8">
        <meta http-equiv="X-UA-Compatible" content="IE=edge">
        <meta name="viewport" content="width=device-width, initial-scale=1">
        <title>Reporting Demo</title>

        <!-- jquery to support bootstrap and for general dom manipulation -->
        <script src="https://code.jquery.com/jquery-2.1.4.min.js"></script>

        <!-- Latest compiled and minified CSS -->
        <link rel="stylesheet" href="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.5/css/bootstrap.min.css">

        <!-- Optional theme -->
        <link rel="stylesheet" href="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.5/css/bootstrap-theme.min.css">

        <!-- Latest compiled and minified JavaScript -->
        <script src="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.5/js/bootstrap.min.js"></script>
        
        <!-- you may need to update these URLs -->
        <link rel="stylesheet" href="./lib/nv.d3.css"></link>
        <script type="text/javascript" src="./dist/xapidashboard.min.js"></script>
        <script type="text/javascript" src="./dist/xapicollection.min.js"></script>
        <script>
            ADL.XAPIWrapper.changeConfig({"endpoint" : 'https://lrs.adlnet.gov/xAPI/', user: 'tom', password: '1234'});

            window.onload = function(){
            };
        </script>
    </head>
    <body>
        <div class="container">
            <h2>Hands on Charts</h2>
            <div id="graphContainer">
               <svg></svg>
            </div>
        </div>
    </body>
</html>
```
## Bar Chart of Users Attempt Count

![Users Attempt Count Bar Chart](img/user-att.png)

## Bar Chart of the Frequency a Number is Guessed

![Guess Number Frequency Bar Chart](img/numfreq.png)

## Extra Challenges
The following challenges gives you the chance to gain more experience querying the LRS and filtering statements. We used the [xAPI Collection](https://github.com/adlnet/xAPI-Dashboard/blob/master/API_collection.md) module, but you may use any process you want. 
#### List the players of ‘guess the number’  
  __Output:__  
  ```javascript  
  [
    {
        "player": "creighton"
    },
    {
        "player": "Pj"
    },
    {
        "player": "<change this>"
    }
  ]
  ```  
    
    
#### List the players and average number of guesses to get the number  
  __Output:__  
  ```javascript
  [
    {
        "name": "creighton",
        "average": 6.5
    },
    {
        "name": "<change this>",
        "average": 5.666666666666667
    }
  ]
  ```  
    
    
#### Display the statement of the longest game played  
  __Output:__  
  ```javascript
  {
    "verb": {
        "id": "http://adlnet.gov/event/2015/xapibootcamp/verb/ended",
        "display": {
            "en-US": "ended"
        }
    },
    "version": "1.0.1",
    "timestamp": "2015-07-02T18:17:05.985Z",
    "object": {
        "definition": {
            "type": "http://adlnet.gov/event/2015/xapibootcamp/activity/type/game",
            "name": {
                "en-US": "Guess the Number Game"
            },
            "description": {
                "en-US": "Simple guess the number game to demonstrate xAPI"
            }
        },
        "id": "http://adlnet.gov/event/2015/xapibootcamp/guess-the-number",
        "objectType": "Activity"
    },
    "actor": {
        "account": {
            "homePage": "http://adlnet.gov/accounts",
            "name": "creighton"
        },
        "objectType": "Agent"
    },
    "stored": "2015-07-02T18:09:09.533813+00:00",
    "result": {
        "extensions": {
            "http://adlnet.gov/event/2015/xapibootcamp/guess-the-number/ext/endedAt": "2015-07-02T18:17:05.985Z",
            "http://adlnet.gov/event/2015/xapibootcamp/guess-the-number/ext/number": 92,
            "http://adlnet.gov/event/2015/xapibootcamp/guess-the-number/ext/min": 1,
            "http://adlnet.gov/event/2015/xapibootcamp/guess-the-number/ext/max": 100,
            "http://adlnet.gov/event/2015/xapibootcamp/guess-the-number/ext/guesses": [
                50,
                92
            ],
            "http://adlnet.gov/event/2015/xapibootcamp/guess-the-number/ext/startedAt": "2015-07-02T18:12:14.037Z"
        }
    },
    "context": {
        "registration": "da238e77-eb5c-4c4d-9d57-9f36d46237db",
        "contextActivities": {
            "category": [
                {
                    "id": "http://adlnet.gov/event/2015/xapibootcamp"
                }
            ],
            "grouping": [
                {
                    "id": "http://adlnet.gov/event/2015/xapibootcamp/dev/web"
                }
            ]
        }
    },
    "id": "c21847ab-aa7a-438e-bb07-81c016a48f43",
    "authority": {
        "mbox": "mailto:tom@example.com",
        "name": "tom",
        "objectType": "Agent"
    }
  }
  ```  
    
    
#### List the players and total time playing the game  
  __Output:__  
  ```javascript
  [
    {
        "player": "creighton",
        "totaltime": 457774
    },
    {
        "player": "<change this>",
        "totaltime": 48374
    }
  ]
  ```  
    
    
#### Group the SCORM to xAPI statements by posttest (activity id: http://adlnet.gov/courses/roses/posttest) test scores  
  __Output:__  
  ```javascript
  [
    {
      "group": "0.25",
      "data": [statements...]
    },
    {
      "group": "0.5",
      "data": [statements...]
    },
    ...
  ]
  ```  
    
    
#### Group the VW Sandbox statements by registration UUID  
  __Output:__  
  ```javascript
    [
      {
        "group": <the registration uuid>,
        "data": [statements...]
      },
      ...
    ]
  ```  
    
    
#### Display the most used verb in the LRS in the past 2 weeks  
  __Output:__  
  ```javascript
  [
    {
        "verbID": "http://adlnet.gov/xapi/verbs/passed(to_go_beyond)",
        "count": 552
    }
  ]
  ```  
    
    
#### Display the most used activity in the LRS in the past 2 weeks  
  __Output:__  
  ```javascript
  [
    {
        "activityID": "act:adlnet.gov/JsTetris_XAPI",
        "count": 247
    }
  ]
  ```  
  
## GitHub Gist of Challenge Solutions
The following gist contain solutions for the above challenges. [here](https://gist.github.com/creighton/9709025906677e57e6f3 )
