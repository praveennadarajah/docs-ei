
## Purpose:
	This application demonstrates how to configure WSO2 Streaming Integrator Tooling for retrieving historical tweets within
	one week from Twitter App using twitter source.
	
## Prerequisites:
* Go to the https://apps.twitter.com/ and create new App
* Select the app created in step 1 and go to "permisstion" tab and select "read&write" permission.
* Go to the "keys and access tokens" tab and generate new access token.
* Collect following value from "keys and access tokens tab".
* Consumer Key
* Consumer Secret
* Access Token
* Access Secret Token
* Update the values of the parameters with these values.
	* Save this sample. If there is no syntax error, the following message is shown on the console:
	     * - Siddhi App TwitterPolling-sample successfully deployed. 
	
	Executing the Sample:
	* Start the Siddhi application by clicking on 'Run'.
	
## Testing the Sample:
* See the terminal that prints the events according to the given source configuaration. This sample is to
retrieve tweets that mentioning "NASA". So the each tweet will contain '@NASA'.

## Viewing the Results:
See the output on the terminal.

```sql
@App:name("TwitterPolling-sample")
@App:description('Retrieving historical tweets within one week')


@source(type='twitter' , consumer.key="xxxxxxxx",consumer.secret='xxxxxxxx', access.token ='xxxxxxxxx',
access.token.secret='xxxxxxxxx', mode='polling', count = '100', query = '@NASA', polling.interval = '300',
@map(type='keyvalue', @attributes(createdAt = 'createdAt', id = 'tweetId', text= 'text', hashtags = 'hashtags',
userMentions = 'userMentions')))
define stream rcvEvents(createdAt string, id long, text string ,hashtags string, userMentions string);

@sink(type='log', prefix='LOGGER')
define stream Outputstream(createdAt string, id long, text string ,hashtags string, userMentions string);

@info(name='TwitterStream')
from rcvEvents
select *
insert into Outputstream;
```