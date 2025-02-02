

## Purpose:
This application demonstrates how to send a single event via Single Simulation and to generate alerts using filters, when the threshold value is exceeded. Furthermore, it shows how to configure WSO2 Streaming Integrator Tooling to publish an alerts via e-mail. An alert is generated when a large valued transaction (The threshold for transaction value is 5000) is occurred and the relevant alert is sent as an e-mail.

## Prerequisites:
1) Replace the Sink configuration values for following options.
- username: business.rules.manager (senders username)
- password: business-rules (senders password)
- address: business.rules.manager@wso2.com (senders address)
- to: template-manager@wso2.com (receivers address)
- subject: subject of the email
2) you need to enable access to "less  secure apps" in the gmail account via "https://myaccount.google.com/lesssecureapps" link
3) Save this sample

## Executing the Sample:
1) Start the Siddhi application by clicking on 'Run'
2) If the Siddhi application starts successfully, the following messages would be shown on the console
* AlertsAndThresholds.siddhi - Started Successfully!.

## Testing the Sample:
1) Click on 'Event Simulator' (double arrows on left tab)
2) Click 'Single Simulation' (this will be already selected)
3) Select AlertsAndThresholds as 'Siddhi App Name'
4) Select TransactionStream as 'StreamName'
5) Provide attribute values
- creditCardNo: 1234567898765432
- country: SL
- item: mobile
- quantity: 100
- price: 5000
6) Click on the start button (Arrow symbol) next to the newly created simulator

* To generate an alert, simulate an event where the multiplied value of 'quantity' and 'price' is greater than 5000. Note the filter we have used in the input stream to generate alerts when threshold value '5000' is exceeded.

## Viewing the Results:
Check the receiver gmail inbox (The gmail referred to in 'to' Sink configuration) to see the alert as follows.
## Subject: Alert for large value transaction: cardNo:1234567898765432
## Content:
## creditCardNo:"1234567898765432",
## country:"SL",
## item:"mobile",
## quantity:100,
## price:5000


```sql
@App:name("AlertsAndThresholds")

@App:description('Simulate a single event and receive alerts as e-mail when a predefined threshold value is exceeded')



define stream TransactionStream(creditCardNo string, country string, item string, quantity int, price double);

@sink(type='email',
      username ='send.alert.account',
      address ='send.alert.account@gmail.com',
      password= 'send.alert.account.password', 
      subject='Alert for large value transaction: cardNo:{{creditCardNo}}',
      to='receive.alert.account1@gmail.com, receive.alert.account2@gmail.com',
      port = '465',
      host = 'smtp.gmail.com',
      ssl.enable = 'true',
      auth = 'true', 
      @map(type='text'))
define stream AlertStream(creditCardNo string, country string, item string, quantity int, price double);

--Filter events when quantity * price > 5000 condition is satisfied
@info(name='query1') 
from TransactionStream[quantity * price  > 5000]
select *
insert into AlertStream;
```