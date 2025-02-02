

## Purpose:
This application demonstrates how to perform join on streaming data with the data stored in RDBMS. The sample depicts a scenario, where a transaction by a credit card with which fraudulent activity has been previously done. The credit card numbers, which were noted for fraudulent activities are stored in an RDBMS table.

## Prerequisites:
1) Ensure that MySQL is installed on your machine
2) Create a database named 'fraudDB' in MySQL. This database is referred to with 'jdbc:mysql://localhost:3306/fraudDB' url.
3) Create a table named 'FraudTable': CREATE TABLE FraudTable (creditCardNo VARCHAR(20));
4) Insert some values to the table : INSERT INTO FraudTable VALUES ("143-90099-23433");
5) In the store configuration of this application, replace 'username' and 'password' values with your MySQL credentials
4) Save this sample

## Executing the Sample:
1) Start the Siddhi application by clicking on 'Run'
2) If the Siddhi application starts successfully, the following messages would be shown on the console
* JoinWithStoredData.siddhi - Started Successfully!

## Testing the Sample:
1) Simulate single events. For this, click on 'Event Simulator' (double arrows on left tab) -> 'Single Simulation' -> Select 'JoinWithStoredData' as 'Siddhi App Name' -> Select 'TradeStream' as 'Stream Name' -> Provide attribute values -> Send
2) Send at-least one event with the single event simulator, where the creditCardNo matches a creditCardNo value in the data we previously inserted to the FraudTable. This would satisfy the 'on' condition of our join query

## Viewing the Results:
See the output for suspicious trades on the console.


```sql
@App:name("JoinWithStoredData")

@App:description('Join streaming data with data stored in an RDBMS table')



@Store(type="rdbms",
       jdbc.url="jdbc:mysql://localhost:3306/fraudDB",
       username="root",
       password="root" ,
       jdbc.driver.name="com.mysql.jdbc.Driver")
@PrimaryKey("creditCardNo")
define table FraudTable (creditCardNo string);

define stream TradeStream(creditCardNo string, trader string, tradeInfo string);

@sink(type='log')
define stream SuspiciousTradeStream(creditCardNo string, suspiciousTrader string, suspiciousInfoTrade string);

--Perform a join on credit card number, to capture transactions with credit cards that have previously been used for fraudulent activity
@info(name='query1')
from TradeStream as t join FraudTable as f
    on t.creditCardNo == f.creditCardNo
select t.creditCardNo, t.trader as suspiciousTrader, t.tradeInfo as suspiciousInfoTrade
insert into SuspiciousTradeStream;
```