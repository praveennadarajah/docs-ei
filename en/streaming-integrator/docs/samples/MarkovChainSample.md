

## Purpose:
This application demonstrates state transition based on an existing Markov matrix, when the transition probability is less than or equal to the alert threshold probability, notify state is changed to 'true' in the event.

## Prerequisites:
Please find the below resource files in {WSO2_SI_Home}/samples/artifacts/MarkovChainSample directory.

1) Copy MarkovChainSimulator.json file to {WSO2_SI_Home}/wso2/server/deployment/simulation-configs
2) Copy MarkovChainTest.csv file to {WSO2_SI_Home}/wso2/server/deployment/csv-files
3) Copy MarkovChainMatrix.csv file to {WSO2_SI_Home}/wso2/server/deployment/csv-files
4) Copy MarkovChainSample.siddhi file to {WSO2_SI_Home}/wso2/server/deployment/siddhi-files/ and replace the absolute file path for MarkovChainMatrix.csv in 'MarkovChainSample' Siddhi App.
5) Start editor runtime.

## Executing the Sample:
1) Start the Siddhi application by clicking on 'Run'
2) If the Siddhi application starts successfully, the following messages would be shown on the console
* MarkovChainSample.siddhi - Started Successfully!.

## Testing the Sample:
1) Click 'Feed Simulation'
2) Run by click on the play button in the 'MarkovChainSimulator'

## Viewing the Results:
Messages similar to the following would be shown on the editor console.
INFO {io.siddhi.core.stream.output.sink.LogSink} - MarkovChainSample : StatePatternStream : Event{timestamp=1513624961461, data=[1, null, testState01, 0.0, false], isExpired=false}
INFO {io.siddhi.core.stream.output.sink.LogSink} - MarkovChainSample : StatePatternStream : Event{timestamp=1513624962461, data=[2, null, testState02, 0.0, false], isExpired=false}
INFO {io.siddhi.core.stream.output.sink.LogSink} - MarkovChainSample : StatePatternStream : Event{timestamp=1513624963461, data=[1, testState01, testState03, 0.3, true], isExpired=false}
INFO {io.siddhi.core.stream.output.sink.LogSink} - MarkovChainSample : StatePatternStream : Event{timestamp=1513624964461, data=[3, null, testState01, 0.0, false], isExpired=false}
INFO {io.siddhi.core.stream.output.sink.LogSink} - MarkovChainSample : StatePatternStream : Event{timestamp=1513624965461, data=[3, testState01, testState02, 0.3, true], isExpired=false}
INFO {io.siddhi.core.stream.output.sink.LogSink} - MarkovChainSample : StatePatternStream : Event{timestamp=1513624966461, data=[1, testState03, testState01, 0.6000000000000001, false], isExpired=false}
INFO {io.siddhi.core.stream.output.sink.LogSink} - MarkovChainSample : StatePatternStream : Event{timestamp=1513624967461, data=[1, testState01, testState02, 0.5333333333333333, false], isExpired=false}
INFO {io.siddhi.core.stream.output.sink.LogSink} - MarkovChainSample : StatePatternStream : Event{timestamp=1513624968461, data=[2, testState02, testState01, 0.3, true], isExpired=false}
INFO {io.siddhi.core.stream.output.sink.LogSink} - MarkovChainSample : StatePatternStream : Event{timestamp=1513624969461, data=[2, testState01, testState03, 0.325, false], isExpired=false}
INFO {io.siddhi.core.stream.output.sink.LogSink} - MarkovChainSample : StatePatternStream : Event{timestamp=1513624970461, data=[4, null, testState01, 0.0, false], isExpired=false}
INFO {io.siddhi.core.stream.output.sink.LogSink} - MarkovChainSample : StatePatternStream : Event{timestamp=1513624971461, data=[4, testState01, testState03, 0.45999999999999996, false], isExpired=false}

* You will notice that when the transition probability is less than or equal to the alert threshold probability, notify state is changed to 'true'

```sql
@App:name("MarkovChainSample")
@App:description('Identify abnormal patterns based on an existing Markov matrix.')


define stream StateStream (id string, state string);
@sink(type="log")
define stream StatePatternStream (id string, lastState string, state string, transitionProbability double, notify bool);

@info(name = 'query1')
from StateStream#markov:markovChain(id, state, 60 min, 0.3, '{WSO2_SI_Home}/wso2/editor/deployment/csv-files/MarkovChainMatrix.csv', false)
select id, lastState, state, transitionProbability, notify
insert into StatePatternStream;
```