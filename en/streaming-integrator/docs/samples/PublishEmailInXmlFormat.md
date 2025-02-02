
## Purpose:
This application demonstrates how to use siddhi-io-email for publishing events to files.

## Prerequisites:
1) Replace the Sink configuration values for following options.
- username: senders username (Ex:- 'example123')
- address: senders address (Ex:- 'example123@wso2.com')
- password: senders password
- to: receivers address (Ex:- 'example987@wso2.com')
- subject: subject of the email
2) You need to enable access to "less secure apps" in the sender's gmail account via "https://myaccount.google.com/lesssecureapps" link
3) Save this sample

## Executing the Sample:
1) Start the Siddhi application by clicking on 'Run'
2) If the Siddhi application starts successfully, the following messages would be shown on the console
* PublishEmailInXmlFormat.siddhi - Started Successfully!.

## Testing the Sample:
1) Click on 'Event Simulator' (double arrows on left tab)
2) Click 'Single Simulation' (this will be already selected)
3) Select PublishEmailInXmlFormat as 'Siddhi App Name'
4) Select SweetProductionStream as 'StreamName'
5) Provide attribute values
- name: chocolate cake
- amount: 10.10
6) Click on the start button (Arrow symbol) next to the newly created simulator

## Viewing the Results:
Check the receiver gmail inbox (The gmail referred to in 'to' Sink configuration) to see the alert as follows.
## Note: current hour depends on the system's timestamp
## Subject: <subject of the email>
## Content: <events><event><name>chocolate cake</name><hourlyTotal>10.1</hourlyTotal><currentHour>0.0</currentHour></event></events>


```sql
@App:name("PublishEmailInXmlFormat")


define stream SweetProductionStream (name string, amount double);

@sink(type='email', @map(type='xml') ,
                    username ='<senders email user name>',
                    address ='<senders email address>',
                    password= '<senders email password>',
                    subject='Test Siddhi-io-email-{{name}}', 
                    to='<receivers email address>',
                    port = '465',
                    host = 'smtp.gmail.com',
                    ssl.enable = 'true',
                    auth = 'true')                
define stream LowProductionAlertStream(name string, hourlyTotal double, currentHour  double);

@sink(type='log')
define stream EmailLogStream(name string, hourlyTotal double, currentHour  double);

from SweetProductionStream#window.time(1 min)
select name, sum(amount) as hourlyTotal,
convert(time:extract('HOUR', time:currentTimestamp(), 'yyyy-MM-dd hh:mm:ss'), 'double') as currentHour
insert into LowProductionAlertStream;

from LowProductionAlertStream
insert into EmailLogStream;
```