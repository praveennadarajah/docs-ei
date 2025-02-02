

## Purpose:
This application demonstrates how to simulate random events via Feed Simulation and calculate running aggregates such as avg, min, max, etc. The aggregation is executed on events within a time window. A sliding time window of 10 seconds is used in this sample. For more information on windows please refer to "https://wso2.github.io/siddhi/documentation/siddhi-4.0/#window". The 'group by' clause helps to perform aggregation on events grouped by a certain attribute. In this sample, the trading information per trader is aggregated and summarized, for a window of 10 seconds.

## Prerequisites:
1) Save this sample

## Executing the Sample:
1) Start the Siddhi application by clicking on 'Run'
2) If the Siddhi application starts successfully, the following messages would be shown on the console
* AggregateOverTime.siddhi - Started Successfully!

## Testing the Sample:
Configure random event simulation as follows
1) Click on 'Event Simulator' (double arrows on left tab)
2) Click 'Feed Simulation' -> 'Create'
3) Give a name (Or the default name in the place holder will be used as its name)
4) Select 'Random' as the 'Simulation Source'
5) Click on 'Add Simulation Source'
6) Select AggregateOverTime as 'Siddhi App Name'
7) Select TradesStream as 'StreamName'
8) For 'trader' change config type to 'Regex based' and give the pattern as '(Bob|Jane|Tom)'. In the example only Bob, Jane and Tom would be selected as trader when generating random events with pattern '(Bob|Jane|Tom)'. Using few values for trader would be helpful to verify the output, since we are grouping by trader.
9) Keep 'Primitive Based' as the config type for quantity
10) Save the simulator configuration
11) The newly created simulator would be listed under 'Active Feed Simulations' of 'Feed Simulation' tab
12) Click on the start button (Arrow symbol) next to the newly created simulator

## Viewing the Results:
See the input and respective output on the console. The output reflects the aggregation for the events sent during last 10 seconds.



```sql
@App:name("AggregateOverTime")

@App:description('Simulate multiple random events and calculate aggregations over time with group by')



define stream TradesStream(trader string, quantity int);

@sink(type='log')
define stream SummarizedTradingInformation(trader string, noOfTrades long, totalTradingQuantity long, minTradingQuantity int, maxTradingQuantity int, avgTradingQuantity double);

--Find count, sum, min, max and avg of quantity per trader, during the last 10 seconds
@info(name='query1')
from TradesStream#window.time(10 sec)
select trader, count() as noOfTrades, sum(quantity) as totalTradingQuantity, min(quantity) as minTradingQuantity, max(quantity) as maxTradingQuantity, avg(quantity) as avgTradingQuantity
group by trader
insert into SummarizedTradingInformation;
```