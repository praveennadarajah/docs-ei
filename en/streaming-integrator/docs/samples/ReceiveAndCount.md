

## Purpose:
This application demonstrates how to configure WSO2 Streaming Integrator Tooling to receive events via HTTP transport and view the output on the console. The count of all events arriving to the stream is calculated.

## Prerequisites:
1) Save this sample

## Executing the Sample:
1) Start the Siddhi application by clicking on 'Run'

2) If the Siddhi application starts successfully, the following messages would be shown on the console
* Source Listener has created for url http://localhost:8006/productionStream
* ReceiveAndCount.siddhi - Started Successfully!

## Notes:
If you edit this application while it's running, stop the application -> Save -> Start.
If the message "Source Listener has created for url http://localhost:8006/productionStream" does not appear,it could be due to port 8006, defined in the Siddhi application is already being used by a different program. To resolve this issue, please do the following,
* Stop this Siddhi application (Click 'Run' on menu bar -> 'Stop')
* Change the port 8006 to an unused port, in this Siddhi application's source configuration.
* Start the application and check whether the specified messages appear on the console

## Testing the Sample:
Publish events to the http endpoint defined by 'receiver.url' in Source configuration. You may publish events with curl command or Postman application as follows,
1) Navigate to {WSO2SIHome}/samples/sample-clients/http-client and run "ant" command as follows:
Run "ant" command in the terminal
If you want to publish custom number of events, you need to run "ant" command as follows
ant -DnoOfEventsToSend=5

2) Publish events with curl command:
Publish few events in json format to the http endpoint as follows (The values for name, age and country attributes can be changed as desired)
curl -X POST -d "{\"event\":{\"name\":\"Cake\",\"amount\":20.12}}" http://localhost:8006/productionStream --header "Content-Type:application/json"

3) Publish events with Postman:
a) Install 'Postman' application from Chrome web store
b) Launch the application
c) Make a 'Post' request to 'http://localhost:8006/productionStream' endpoint. Set the Content-Type to 'application/json' and set the request body in json format as follows,
{
"event": {
"name": "Cake",
"amount": 20.12
}
}

## Viewing the Results:
See the output on the console. Note how the count increments with every event you send.


```sql
@App:name("ReceiveAndCount")

@App:description('Receive events via HTTP transport and view the output on the console')


@Source(type = 'http',
        receiver.url='http://localhost:8006/productionStream',
        basic.auth.enabled='false',
        @map(type='json'))
define stream SweetProductionStream (name string, amount double);

@sink(type='log')
define stream TotalCountStream (totalCount long);

-- Count the incoming events
@info(name='query1')
from SweetProductionStream
select count() as totalCount
insert into TotalCountStream;
```