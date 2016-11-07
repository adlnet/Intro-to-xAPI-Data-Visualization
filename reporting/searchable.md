# Reporting


## Searchable Properties in a Statement
The following table lists the properties of a statement that can be used to query the LRS. See the xAPI spec [for more details](https://github.com/adlnet/xAPI-Spec/blob/master/xAPI-Communication.md#213-get-statements).

### id
  >__GET Params:__ statementId  
  >__Example:__ /statements?statementId=9847e454-6ae7-4d00-ba6b-04f209171de6  
  >__GET Params:__ voidedStatementId  
  >__Example:__ /statements?voidedStatementId=9847e454-6ae7-4d00-ba6b-04f209171de6  

### actor/object
  >__GET Params:__ agent  
  >__Example:__ /statements?agent={"mbox":"mailto:learner@example.com"}  

### verb  
  >__GET Params:__ verb  
  >__Example:__ /statements?verb=http://adlnet.gov/expapi/verbs/experienced  
  
### object <small>(when it is an Activity)</small>  
  >__GET Params:__ activity  
  >__Example:__ /statements?activity=http://adlnet.gov/event/xapiworkshop/tecom/guess-the-number  
  
### stored
  >__GET Params:__ since or until  
  >__Example:__ /statements?since=2015-07-02T11:59:19.353Z  
  >__NOTE:__ since parameter is exclusive, until parameter is inclusive

### context properties
#### registration
  >__GET Params:__ registration  
  >__Example:__ /statements?registration=9bb3f7a2-91eb-44a4-ac18-761b11160292  

#### instructor or team
  >__GET Params:__ agent and related_agents  
  >__Example:__ /statements?agent={"mbox":"mailto:learner@example.com"}&related_agents=true  

#### contextActivities
  >__GET Params:__ activity and related_activities  
  >__Example:__ /statements?activity=http://adlnet.gov/event/xapibootcamp/tecom&related_activities=true  

### authority
  >__GET Params:__ agent and related_agents
  >__Example:__ /statements?agent={"mbox":"mailto:admin@example.com"}&related_agents=true
