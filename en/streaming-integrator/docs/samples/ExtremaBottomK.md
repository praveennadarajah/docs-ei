
## Purpose:
This application demonstrates how to use the siddhi-execution-extrema with bottomK function.

## Prerequisites:
1) Start editor.
2) Save this sample.

## Executing the Sample:
1) Start the Siddhi application by clicking on 'Run'
2) If the Siddhi application starts successfully, the following messages would be shown on the console,
* ExtremaBottomK.siddhi - Started Successfully!

## Testing the Sample:
Option1: Publish events with curl command:
Publish few events in json format to the http endpoint as follows (The values for name and amount attributes can be changed as desired)
curl -X POST -d "{\"event\": {\"name\":\"sugar\",\"amount\": 20}}"  http://localhost:8006/productionStream --header "Content-Type:application/json"

Option2: Publish events with Postman:
a) Install 'Postman' application from Chrome web store.
b) Launch the application.
c) Make a 'POST' request to 'http://localhost:8006/productionStream' endpoint. Set the Content-Type to 'application/json' and set the request body in json format as follows,
{
"event": {
"name": "sugar",
"amount": 20
}
}
d) Send some more events in the same format, with different values for "name" and "amount".

Option3: Publish events with http sample client:
1) Navigate to {WSO2SIHome}/samples/sample-clients/http-client and run the following command
ant -Dtype=json -DfilePath={WSO2SIHome}/samples/artifacts/ExtreamBottomK/ExtremaBottomKEvents.txt -DeventDefinition='{"event":{"name":"{0}","amount":{1}}}' -Durl=http://localhost:8006/productionStream

## Viewing the Results:
See the output on ExtremaBottomK console.
Expected output
INFO {io.siddhi.core.stream.output.sink.LogSink} - ExtremaBottomK : outputStream : [Event{timestamp=1529498254202, data=[sugar, 20, sugar, 1, null, null, null, null], isExpired=false}, Event{timestamp=1529498254202, data=[cake, 10, cake, 1, sugar, 1, null, null], isExpired=false}]
INFO {io.siddhi.core.stream.output.sink.LogSink} - ExtremaBottomK : outputStream : [Event{timestamp=1529498262769, data=[cake, 10, cake, 1, sugar, 1, null, null], isExpired=false}, Event{timestamp=1529498262769, data=[toffee, 65, toffee, 1, cake, 1, sugar, 1], isExpired=false}]
INFO {io.siddhi.core.stream.output.sink.LogSink} - ExtremaBottomK : outputStream : [Event{timestamp=1529498270897, data=[toffee, 65, toffee, 1, cake, 1, sugar, 1], isExpired=false}, Event{timestamp=1529498270897, data=[cake, 74, toffee, 1, sugar, 1, cake, 2], isExpired=false}]
INFO {io.siddhi.core.stream.output.sink.LogSink} - ExtremaBottomK : outputStream : [Event{timestamp=1529498278304, data=[cake, 74, toffee, 1, sugar, 1, cake, 2], isExpired=false}, Event{timestamp=1529498278304, data=[toffee, 25, sugar, 1, toffee, 2, cake, 2], isExpired=false}]

```sql
@App:name("ExtremaBottomK")
@App:Description('Demonstrates how to use the siddhi-execution-extrema with bottomK function')


@Source(type = 'http', receiver.url='http://localhost:8006/productionStream', basic.auth.enabled='false',
    @map(type='json'))
define stream inputStream (name string, amount long);

@sink(type='log')
define stream outputStream(name string, amount long, bottom1Element string, bottom1Frequency long, bottom2Element string, bottom2Frequency long, bottom3Element string, bottom3Frequency long);

from inputStream#extrema:bottomK(name, 3)
insert all events into outputStream;
```