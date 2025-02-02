

## Purpose:
This application demonstrates Siddhi events priority concept. In the sample we keep the track of events in UserWallPostStream base on their priority value.
After every 1 sec, priority of every events will be reduced by 1 and the updated priority will be sent out with the last known attributes of those events.

## Prerequisites:
1) Download siddhi-execution-priority-5.0.1.jar and copy the jar to  {WSO2SIHome}/lib
## https://maven.wso2.org/nexus/service/local/repositories/releases/content/org/wso2/extension/siddhi/execution/priority/siddhi-execution-priority/5.0.1/siddhi-execution-priority-5.0.1.jar
2) Save this sample

## Executing the Sample:
1) Start the Siddhi application by clicking on 'Run'
2) If the Siddhi application starts successfully, the following messages would be shown on the console
* PriorityExtensionSample.siddhi - Started Successfully!

## Notes:
If you edit this application while it's running, stop the application -> Save -> Start.

## Testing the Sample:
You may send events to UserWallPostStream, via event simulator
a) Open event simulator by clicking on the second icon or press Ctrl+Shift+I.
	b) In the Single Simulation tab of the panel, select values as follows:
* Siddhi App Name  : PriorityExtensionSample
* Stream Name     : UserWallPostStream
c) Enter following values in the fields and send
## userId:  "Mohan",
## ext:  "Hello World!",
## priority:  "1"
d) Enter following values in the fields and send
## userId:  "Nuwan",
## ext:  "Good Morning!",
## priority:  "3"

## Viewing the Results:
Messages similar to the following would be shown on the editor console.

INFO {io.siddhi.core.stream.output.sink.LogSink} - PriorityExtensionSample : PrioritizedUserWallPostStream : Event{timestamp=1513620904666, data=[Mohan, Hello World!, 1, Mohan, 1], isExpired=false}
INFO {io.siddhi.core.stream.output.sink.LogSink} - PriorityExtensionSample : PrioritizedUserWallPostStream : Event{timestamp=1513620905670, data=[Mohan, Hello World!, 1, Mohan, 0], isExpired=false}
INFO {io.siddhi.core.stream.output.sink.LogSink} - PriorityExtensionSample : PrioritizedUserWallPostStream : Event{timestamp=1513620924365, data=[Nuwan, Good Morning, 3, Nuwan, 3], isExpired=false}
INFO {io.siddhi.core.stream.output.sink.LogSink} - PriorityExtensionSample : PrioritizedUserWallPostStream : Event{timestamp=1513620925370, data=[Nuwan, Good Morning, 3, Nuwan, 2], isExpired=false}
INFO {io.siddhi.core.stream.output.sink.LogSink} - PriorityExtensionSample : PrioritizedUserWallPostStream : Event{timestamp=1513620926373, data=[Nuwan, Good Morning, 3, Nuwan, 1], isExpired=false}
INFO {io.siddhi.core.stream.output.sink.LogSink} - PriorityExtensionSample : PrioritizedUserWallPostStream : Event{timestamp=1513620927378, data=[Nuwan, Good Morning, 3, Nuwan, 0], isExpired=false}

* You will notice that every 1 sec priority of the userId "Mohan" and "Sajith" reduced by 1 and it will continue until their priority reduced to 0.

```sql
@APP:name('PriorityExtensionSample')

@App:description('Keeps track of the priority of events in a stream.')


define stream UserWallPostStream (userId string, text string, priority long);

@sink(type='log')
define stream PrioritizedUserWallPostStream (userId string, text string, priority long, priorityKey string, currentPriority long);

from UserWallPostStream#priority:time(userId, priority, 1 sec)
select *
insert all events into PrioritizedUserWallPostStream;
```