
![image](https://user-images.githubusercontent.com/78746011/147705798-8859cf35-673a-45b9-b386-d65d64c57d98.png)

# Overview
In a day of office suites being replaced by slack channels, we figured it only appropriate to share just one of the many ways that the Slack APIs and SignalWire APIs can be used in unison. This application will associate your SignalWire phone number and your Slack Workspace so that you may send and receive messages all through the Slack interface.

<br/>

# What do you need to run this code?

View the full code on our Github [here](https://github.com/jongray00/SignalWire_Slack)!

This guide uses the Python SignalWire SDK, for a guide on installation click [here](https://developer.signalwire.com/twiml/reference/client-libraries-and-sdks#python).

You will also need to have a SignalWire phone number, and have your SignalWire Space credentials ready to go (
API Token, Space URL, and Project ID**). You can find all of these credentials in an easily-copyable format under the **API** tab of your SignalWire Space. For more information on where to find this stuff, check out our [Navigating your SignalWire Space](doc:navigating-your-space) section!

[Ngrok](https://developer.signalwire.com/apis/docs/how-to-test-webhooks-with-ngrok) will be used to expose our application to both SignalWire and Slack via webhooks.

Lastly, you will need a slack account and workspace. You can sign up for a free account [here](https://slack.com/get-started#/createnew)

<br/>

# How to Run the Application

## Configure Slack and SignalWire Webhooks

The first goal to accomplish is to configure our Slack and SignalWire webhooks so that all events that occur over either platform will be sent to our application. In order to complete the configuration of the slackbot, we must enter a webhook that verifies a `challenge` request. So, we will begin by setting this up and then continue on with configuring the slackbot.

In order to create our webhooks, we must run our application as well as set up our ngrok tunnel. Our ngrok tunnel will give us the base of our webhook and then the application will define the path that we must follow.

Begin by installing all necessary packages. You can accomplish this by running the following command in the terminal of your application's root directory.
``` pip install flask signalwire twilio slackclient python-dotenv ```

Once all packages are installed, run the application by entering the following command in your terminal.
``` python main.py ```

Now that our application is running, we can see it live on `http://localhost:5050` so we must tunnel it using ngrok to be exposed to the internet. Once you have ngrok installed, you can run the following command in your terminal.
``` ngrok http 5050 ```


Your terminal will then transform into an ngrok web interface for our application. From the screenshot below, we can see that our two webhooks **for this example** will have the domain `https://eebe18050de2.ngrok.io` and then can be differentiated by appending `/slack` or `/signalwire`, as seen in the app.py file. 

With the ngrok standard account your forwarded domain will be an arbitrary scramble of alphanumeric characters which change every time you run ngrok so be sure to keep your tunnels running, or update your webhooks as needed.
![image](https://user-images.githubusercontent.com/78746011/147705870-c607a710-6af2-4853-8e4c-6d43ade47341.png)<br/>


## Set Up Slack Bot

Now that we have our slack webhook, we are ready for all of the steps to create our slackbot. After you have created your account, navigate to your [Slack apps dashboard](https://api.slack.com/apps) and select **Create New App** then **From Scratch** . Continue on with naming your bot and assign it to the appropriate workspace. 
![image](https://user-images.githubusercontent.com/78746011/147705886-cedb09e3-64a8-496b-a2ed-00afa96e2ba7.png)<br/>

<br/>

Once you have created your application, you should see a page that looks like this.

![image](https://user-images.githubusercontent.com/78746011/147705920-3cc7c533-229d-45ca-b903-a10ae7ee62af.png)<br/>



<br/>

We must then set up the appropriate [Scopes](https://api.slack.com/scopes), which is the slack equivalent of allowing specific permissions. To do this, click on **Permissions** and then scroll down to **Bot Token Scopes** . Enter the following OAuth Scopes:
* channels:history
* channels:join
* channels:read
* chat:write
* chat:write.customize
* chat:write.public


<br/>

![image](https://user-images.githubusercontent.com/78746011/147705939-e8fda5bb-cd9a-442a-a42b-a32a02e4e25a.png)


<br/>


Now that we have given our slackbot permission to do just about whatever it wants in slack, it is time to revisit the webhook we created previously through ngrok. We can connect this slackbot to our application by navigating to ** Event Subscriptions ** on the left menu and enable event subscriptions. This is the slack equivalent to a [status callback](https://developer.signalwire.com/apis/docs/signalwire-status-callbacks) within SignalWire.

From the configuring webhooks section, we noted that our slack webhook would be `https://eebe18050de2.ngrok.io/slack` for this example. Your Request URL will look similar but your own ngrok domain will need to be used.

<br/>

![image](https://user-images.githubusercontent.com/78746011/147705948-0eee32ac-b920-4f52-b4e7-6c92402d292a.png)

As noted in the above screenshot, you will need to scroll to ** Subscribe to events on behalf of users ** and subscribe to:
* message.channels


Lastly, click  Save Changes  and it is time to go live with our bot. 

Navigate back to Basic Information and click on  Install Your App to generate your Slack API Credentials, specifically the Bot User OAuth Token.

Take a note of the `Bot User OAuth Token` because this will later be entered into our .env file.

![image](https://user-images.githubusercontent.com/78746011/147705960-bb617df1-ed3c-4a2b-915c-68bda28b677e.png)


<br/>


## Connecting to SignalWire

Now that Slack is in communication with our application, we must now associate a SignalWire phone number that will be using Slack as the front end for their conversational messaging. How will we connect this? You guessed it, the webhook we created earlier!

If you need to [Create a SignalWIre Account](https://m.signalwire.com/signups/new?s=1) or [Purchase a number](https://developer.signalwire.com/apis/docs/buying-a-phone-number) please so now.

Within your SignalWire dashboard, we can associate this webhook to our phone number by navigating to  Phone Numbers  and then  Edit Settings .

We will only be dealing with messaging so by scrolling down to  Messaging Settings  we can handle incoming messages using LaML Webhooks and then entering our ngrok domain with the `/signalwire` appended. And make sure to  Save !

![image](https://user-images.githubusercontent.com/78746011/147705975-ee14eb34-7e4b-4744-827a-977055c18105.png)
        "https://files.readme.io/06ae2df-Screen_Shot_2021-12-29_at_12.43.09_PM.png",

<br/>

You can now test this application out by messaging your SignalWire number from your own device and look out for messages in the #general channel.  You must reply in a thread to the incoming message to initiate a conversation


As a final step, ensure that you add your bot to your Slack channel by going into your channel's settings. Otherwise, your application will not be notified of replies in the Slack channel.


![image](https://user-images.githubusercontent.com/78746011/147705987-8311c8f5-7ec3-414e-b7ce-20b3ca315b69.png)

<br/>

# Step By Step Code Walkthrough

Within the Github repository you will find 2 files that we will be looking at:
* example.env
* app.py


## Setup Your Environment File

1. Copy from example.env and fill in your values
2. Save new file called .env

Your file should look something like this
```
SLACK_BOT_TOKEN=xoxb-XXXXXXXXXXXX-XXXXXXXXXXXXXXX-XXXXXXXXXXXXXXXXX
SIGNALWIRE_PROJ_ID=649dc08e-3558-XXXX-XXXX-XXXXXXXXXXXXXX
SIGNALWIRE_API_TOKEN=PT0THISISAVERYLONGCODETHATBEGINSWITHPT
SIGNALWIRE_NUMBER=+1808444XXXX
SIGNALWIRE_SPACE_URL=EXAMPLE.signalwire.com
```

<br/>


## Walkthrough of app.py

The beginning to our python code is simply starting our flask app and initializing our SignalWIre/ Slack Clients. We will use these clients to tap into the individual SDKs later in the code.  You will notice that the Slack and SignalWire APIs are not very different from eachother.

``` python
load_dotenv()
app = Flask(__name__)

# Declare our Slack and SignalWire
slack_token =os.getenv("SLACK_BOT_TOKEN")
slack_client = slack.WebClient(slack_token)
client = signalwire_client(os.getenv("SIGNALWIRE_PROJ_ID"),os.getenv("SIGNALWIRE_API_TOKEN"),signalwire_space_url = os.getenv('SIGNALWIRE_SPACE_URL'))
```

<br/>

Next, we define the `/signalWire` route to our application. This is the route that is taken when a message is sent to your SignalWire number, and is then needed to be posted to slack.

This flow begins by reading the From and Body of a message, which was sent to our application via JSON file from SignalWire. These values are then stored and are used to post the message to slack from the Slack SDK. The explicit API call to send a chat message is seen on line 6 with `slack_message = slack_client.chat_postMessage()`.


``` python
@app.route('/signalwire', methods=['POST'])
def signalwire_to_slack_flow():
    from_number = request.form['From']
    sms_message = request.form['Body']
    message = f"Text message from {from_number}: {sms_message}"
    slack_message = slack_client.chat_postMessage(
        channel='#general', text=message, icon_emoji=':robot_face:')
    response = MessagingResponse()
    return Response(response.to_xml(), mimetype="text/html"), 200
```



Next, we need to define the `/slack` route. This is going to be the route taken for when an event occurs on Slack, so we need to take this response apart, make sense of it, and then use SignalWire's [Create a Message API](https://developer.signalwire.com/compatibility-api/reference/create_message) to send a text message back to the user.

Notice In line 6, we see the first filtration of JSON responses with the implementation of `parse_message` but we will further discuss that function later. Once the response has gone through and passed all the tests, we can then extract the body and to number to be entered into our Create a Message API.

``` python
@app.route('/slack', methods=['POST'])
def slack_to_signalwire_flow():
    attributes = request.get_json()
    if 'challenge' in attributes:
        return Response(attributes['challenge'], mimetype="text/plain")
    incoming_slack_message_id, slack_message, channel = parse_message(attributes)
    if incoming_slack_message_id and slack_message:
        to_number = get_customer_number(incoming_slack_message_id, channel)
        if to_number:
            to_number = '+' + to_number
            messages = client.messages.create(
                to=to_number, from_=os.getenv("SIGNALWIRE_NUMBER"), body=slack_message)
        return Response(), 200
    return Response(), 200
```


The remainder of the code are some filler functions that make these routes possible. I want to shine some light on the parse_message function because this is what aided greatly in the `/slack` route to determine when to trigger the SignalWire APIs. 

Not all events that occur in a channel need to warrant a text message being sent. What this means is that the Slack API sends a lot of information (JSON data) that is not necessarily needed. So, a filter was created that would only return the attributes of events we care about, and only pulling the information that we need from them.

If a message is sent in a thread, then we are able to dig through the individual attributes of the message to ensure that we are replying back to the correct number and conversations can stay consistent.

```python
def parse_message(attributes):
    if 'event' in attributes and 'thread_ts' in attributes['event']:
        return attributes['event']['thread_ts'], attributes['event']['text'], attributes['event']['channel']
    return None, None, None
  
def get_customer_number(incoming_slack_message_id, channel):
    data = slack_client.conversations_history(channel=channel, latest=incoming_slack_message_id, limit=1, inclusive=1)
    if 'subtype' in data['messages'][0] and data['messages'][0]['subtype'] == 'bot_message':
        text = data['messages'][0]['text']
        phone_number = pull_number_from_slack_message(text)
        return phone_number
    return None

def pull_number_from_slack_message(text):
    data = re.findall(r'\w+', text)
    if len(data) >= 4:
        return data[3]
    return None

if __name__ == '__main__':
    app.run( port=5050)

```

<br/>

# Wrap Up 
As our workplaces change, so do our methods of telecommunication. Our goal here at SignalWire is to simplify your telecommunication methods so that they can all work symbiotically, this begins with powerful API integrations.

In the same way that your web server can be notified of events occurring in your SignalWire dashboard, your web server can be notified of events occurring within Slack to trigger actions within each other's platforms in real-time. This means the possibilities are endless! 

Whether you want to implement a Slack alert for when you run into an error, receive a voicemail, or even when you hit your sales call goal for the day, we are excited to see how you elevate your SignalWire experience with this new integration.



## Resources
[Github Repo](https://github.com/jongray00/SignalWire_Slack)
[Slack apps dashboard](https://api.slack.com/apps)
[Python SignalWire SDK](https://developer.signalwire.com/compatibility-api/reference/client-libraries-and-sdks#python)
[Ngrok](doc:how-to-test-webhooks-with-ngrok)

<br/>

# Sign Up Here

If you would like to test this example out, you can create a SignalWire account and space [here](https://m.signalwire.com/signups/new?s=1).

Please feel free to reach out to us on our [Community Slack](https://join.slack.com/t/signalwire-community/shared_invite/zt-sjagsni8-AYKmOMhP_1sVMvz9Ya_r0Q) or create a Support ticket if you need guidance!
