

## Purpose:
This application demonstrates how to train a ML model with CSV data and do predictions based on it. The sample explores a scenario where the buying/ site browsing patterns, and average monthly earnings per person are used to predict how much she can be expected to spend on buying goods from a certain store per month.

## Prerequisites:
1. Download siddhi-gpl-execution-streamingml-x.x.x.jar from the following link and copy the jar to  {WSO2SIHome}/lib
## http://maven.wso2.org/nexus/content/repositories/wso2gpl/org/wso2/extension/siddhi/gpl/execution/streamingml/siddhi-gpl-execution-streamingml/
2) Shutdown the server and copy this jar file to {WSO2SIHome}/lib location. Re-start the server
3) Save this sample

## Executing the Sample:
1) Start the Siddhi application by clicking on 'Run'
2) If the Siddhi application starts successfully, the following messages would be shown on the console,
* StreamingRegressor.siddhi - Started Successfully!

## Notes:
If you edit this application while it's running, stop the application -> Save -> Start.

## Testing the Sample:
1) Train the ML model with training data set. The '{WSO2SIHome}/samples/artifacts/StreamingRegressor/train.csv' file is used to create the ML model. To simulate the events in this CSV file, execute the following steps,
a) Click on 'Event Simulator' (double arrows on left tab)
b) Click 'Feed Simulation' -> 'Create'
c) Give a name, description and time interval
d) Select 'CSV file' as the 'Simulation Source'
e) Click on 'Add Simulation Source'
f) Select StreamingRegressor as 'Siddhi App Name'
g) Select 'TrainInputStream' as 'StreamName'
h) Click on 'Upload' button under 'CSV file' section and upload the {WSO2SIHome}/samples/artifacts/StreamingRegressor/train.csv file
i) Give ',' as the delimiter
j) Save the configuration
2) Click on the start button (Arrow symbol) next to the newly created simulator, and wait few seconds for the model training to complete
3) Send events to 'PredictionInputStream' using '{WSO2SIHome}/samples/artifacts/StreamingRegressor/test.csv' file, to see the predicted values per each event in test.csv. CSV simulation can be done as explained above

## Viewing the Results:
See the predicted values per each event on the console

## Note:
Stop this Siddhi application, once you are done with the execution


```sql
@App:name("StreamingRegressor")

@App:description('Train a machine learning regressor model with CSV data and subsequently do predictions using that model')


define stream TrainInputStream(avgClothShoppingTimesPerMonth double, avgCosmeticsShoppingTimesPerMonth double, avgEarningsPerMonth double, avgSiteBrowsingPerMonth double, AvgAmountSpentPerMonth double );

define stream PredictionInputStream(avgClothShoppingTimesPerMonth double, avgCosmeticsShoppingTimesPerMonth double, avgEarningsPerMonth double, avgSiteBrowsingPerMonth double);

@sink(type='log')
define stream PredictionOutputStream(prediction double, meanSquaredError double);

--Train the ML model
@info(name = 'query-train')
from TrainInputStream#streamingml:updateAMRulesRegressor('model1', avgClothShoppingTimesPerMonth, avgCosmeticsShoppingTimesPerMonth, avgEarningsPerMonth, avgSiteBrowsingPerMonth, AvgAmountSpentPerMonth)
select meanSquaredError
insert into TrainOutputStream;

--Predict using the model1 created with query 'query-train'
@info(name = 'query-predict')
from PredictionInputStream#streamingml:AMRulesRegressor('model1', avgClothShoppingTimesPerMonth, avgCosmeticsShoppingTimesPerMonth, avgEarningsPerMonth, avgSiteBrowsingPerMonth )
select prediction, meanSquaredError
insert into PredictionOutputStream;
```