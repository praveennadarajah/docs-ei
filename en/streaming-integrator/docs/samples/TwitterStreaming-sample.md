
## Purpose:
	This application demonstrates how to configure WSO2 Streaming Integrator Tooling for retrieving tweets from Twitter App
	using twitter source.
	
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
	     * - Siddhi App TwitterStreaming-sample successfully deployed. 
	
	Executing the Sample:
	* Start the Siddhi application by clicking on 'Run'.
	
## Testing the Sample:
* See the terminal that prints the events according to the given source configuration. This sample is to retrieve
weets that includes the given words. So the events will contain the given keywords.

## Viewing the Results:
See the output on the terminal.


```sql
@App:name("TwitterStreaming-sample")
@App:description('Retrieving real time tweets')


@source(type='twitter' , consumer.key="xxxxxxxxx",consumer.secret='xxxxxxxxxx',access.token ='xxxxxxxx',
access.token.secret='xxxxxxxxx', mode='streaming', track = 'Amazon,Google',
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