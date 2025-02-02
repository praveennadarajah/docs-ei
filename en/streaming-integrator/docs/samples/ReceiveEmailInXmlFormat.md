
## Purpose:
This application demonstrates how to use siddhi-io-email for receiving events from emails.

## Prerequisites:

1) Add relevant siddhi-io-email and siddhi-map-xml jars to the {WSO2Home}/lib folder if not exist.

--------------    Working with the Siddhi editor   ------------------------

1) Make sure you have provide less secure access to the sender's email account.
## eg: For gmail this can be done by visiting 'https://myaccount.google.com/lesssecureapps'

2) Edit the siddhi app by providing following details.
- receiver_email_username
- receiver_email_password

3) Give the subject of the email to <subject_of_mail>. For further information search.terms refer https://wso2-extensions.github.io/siddhi-io-email/api/1.0.9/

3) Save this sample.

## Executing the Sample:
1) Start the Siddhi application by clicking on 'Run'.

2) If the Siddhi application starts successfully, the following messages would be shown on the console.
* ReceiveEmailInXmlFormat.siddhi - Started Successfully!

3) Send an email with the body in following format to receiver's email address.
<events><event><name>WSO2</name><amount>34.56</amount></event></events>

4) The message should be logged in the console.

```sql
@App:name("ReceiveEmailInXmlFormat")


@source(type='email', @map(type='xml'),
username='<receiver_username>',
password='<receiver_email_password>',
store = 'imap' ,
host = 'imap.gmail.com',
folder = 'INBOX',
ssl.enable = 'true' ,
polling.interval = '30' ,
search.term = 'subject:<subject_of_mail>' ,
content.type = 'text/plain')
define stream SweetProductionStream (name string, amount double);

@sink(type='log')
define stream LowProductionAlertStream (name string, hourlyTotal double, currentHour double);

from SweetProductionStream
select name, sum(amount) as hourlyTotal,
convert(time:extract('HOUR', time:currentTimestamp(), 'yyyy-MM-dd hh:mm:ss'), 'double') as currentHour
insert into LowProductionAlertStream;
```