---
layout: post
title:  "Posting messages to Slack using incoming webhooks and Python3 Requests API"
number: 56
date:   2017-09-20 0:00
categories: ninja
---
I wanted to make a short post on using Slack’s incoming webhooks feature to post messages to Slack using the [human-friendly Requests API](http://docs.python-requests.org/en/master/) in Python3.

First things first. You’re going to need to set up incoming webhooks in your Slack account, which you can do [by going here](https://my.slack.com/services/new/incoming-webhook/). An incoming webhook is simply a cryptic and unique URL which can take JSON-formatted POST requests and use that to post to your Slack account. This URL does not require you to authenticate, so please do not share it with anyone.

When you set it up, you will need to configure the default channel that the webhook will post to. For now, I’ll use the private @slackbot channel. You can change this dynamically in the JSON payload you will send to the webhook later on.

<img src="{{ site.images-path | prepend: site.baseurl | prepend: site.url }}posting-messages-to-slack-using-incoming-webhooks-webhook-url.jpg" width="700" height="228" alt="Setting up incoming webhooks in Slack for use with Python.">

Once you set it up, you should get a webhook URL like this:

<img src="{{ site.images-path | prepend: site.baseurl | prepend: site.url }}posting-messages-to-slack-using-incoming-webhooks-setting-up-channel.jpg" width="700" height="83" alt="Your incoming webhooks URL after you set it up. Please keep this safe, and don\'t share it with anyone.">

Keep this URL somewhere safe.

Once you have the above URL, you can write a simple Python3 script like so:

```python
import requests
import json

if __name__ == '__main__':

    wekbook_url = '--your-webhook-url-goes-here--'

    data = {
        'text': 'I am putting myself to the fullest possible use, which is all I think that any conscious entity can ever hope to do.',
        'username': 'HAL',
        'icon_emoji': ':robot_face:'
    }

    response = requests.post(wekbook_url, data=json.dumps(
        data), headers={'Content-Type': 'application/json'})

    print('Response: ' + str(response.text))
    print('Response code: ' + str(response.status_code))

```

Place the above code in a `.py` file, put your webhook URL in `webhook_url`, and run the file using `python3 <file.py>`. You should see this:

```bash
Response: ok
Response code: 200
```

The above response means the message was successfully posted. Check your `slackbot` channel in Slack, under `Direct Messages`, and you should see this:

<img src="{{ site.images-path | prepend: site.baseurl | prepend: site.url }}posting-messages-to-slack-using-incoming-webhooks-hal-message.jpg" width="700" height="40" alt="If you manage to set up Slack and Python successfully, you can finally know what AI think.">

And it’s really as simple as that: get your webhook URL and begin POSTing data in JSON format to that URL. You can find more configuration options [here](https://api.slack.com/incoming-webhooks), including error codes.

Source code for the script can be found [here](https://github.com/ayush-sharma/infra_helpers/blob/master/general/post_to_slack.py).