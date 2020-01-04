---
layout: post
title:  "A Guide to Web Scraping in Python using BeautifulSoup"
number: 73
date:   2018-09-18 0:00
categories: development
---
A few days ago, I wrote down some notes on building a [Mastodon bot using Python]({% post_url 2018-09-06-tweet-toot-building-a-bot-for-mastodon-using-python %}). The bot, called TweetToot, pulled tweets from a Twitter account and reposted the content on the Mastodon social network. I used the **BeautifulSoup** Python library to extract them from the HTML content of the page. Since this functionality was so useful, I thought it would be a separate guide to it.

Today we'll discuss how to use the BeautifulSoup library to extract content from an HTML page and convert it to a list or a dictionary we can use in Python.

## What is web scraping, and why do I need it?

The simple answer is that not every website has an API that provides us with the data in a code-consumable format. Not every website has a separate URL that returns its content in JSON. For example, you might want to get the recipes from your favourite cooking website, or tweets from a Twitter account, or photos from a Pinterest account. Without an API, extracting the HTML, or **scraping**, might be the only way to get that content. I'm going to show you how to do just that in Python.

Please note that not all websites take kindly to their content being scraped, and some have terms and conditions specifically prohibiting web scraping. Please verify that the website you're scraping is okay with it before actually doing it.

## How do I scrape a website in Python?

In order for web scraping to work in Python, we're going to perform 3 basic steps:

1. Extract the HTML content using the **Requests** library.
2. Analyse the HTML structure of the website and identify the HTML tags that our content is in.
3. Create a Python dictionary from the HTML using the **BeautifulSoup** library.

## Installing the libraries

Let's first install the libraries we'll need:

The **Requests** library will help us fetch the HTML content from a website, and **BeautifulSoup** will help us parse it and convert it to a Python dictionary.

Let's go ahead and install these for Python 3:

```
pip3 install requests beautifulsoup4
```

## Extracting the HTML

For this example, let's extract the tweets from a Twitter account. I'm going to pick [The Onion](https://twitter.com/TheOnion) for this example. The URL for this account is:

```
https://twitter.com/TheOnion
```

We can get the HTML content from this page using **Requests**:

```python
url = 'https://twitter.com/TheOnion'

data = requests.get(url)

print(data.text)
```

The variable `data` will contain the HTML source code of the page.

## Extracting content from the HTML

To extract the tweets from the above HTML content, we're first going to need to know which HTML tag the tweets are contained within. We can find this out by examining the HTML source using our web browser's Developer Tools.

<img src="{{ site.images-path | prepend: site.baseurl | prepend: site.url }}a-guide-to-web-scraping-in-python-using-beautifulsoup.jpg" width="730" height="587" alt="A Guide to Web Scraping in Python using BeautifulSoup">

From the image above, we can see that the tweets are nested within a `div` with `id=“timeline”`, and the tweets themselves are within `li` tags with `class=“stream-item”`. We can use these two properties to extract the tweets.

```python
all_tweets = []

html = BeautifulSoup(data.text, 'html.parser')
timeline = html.select('#timeline li.stream-item')

for tweet in timeline:

    tweet_id = tweet['data-item-id']
    tweet_text = tweet.select('p.tweet-text')[0].get_text()

    all_tweets.append({"id": tweet_id, "text": tweet_text})
```

The above code will extract the tweets using the HTML properties we've identified, and put them in the `all_tweets` dictionary. The dictionary will contain key-value pairs:

```python
[{
    "id": "969621896097607685",
    "text": "Report: We Don't Make Any Money If You Don't Click The Fucking Link https://trib.al/FMhL4pm\xa0pic.twitter.com/9tQeT8YXOx"
}, {
    "id": "1041778849263042561",
    "text": "Dwayne “The Rock” Johnson said WHAT?!pic.twitter.com/2HouvPi9Nv"
}, {
    "id": "1041893094273286144",
    "text": "For more exemplary journalism, visit http://theonion.com\xa0.pic.twitter.com/uXmfnF1th8"
}, {
    "id": "1041885234860580864",
    "text": "Supposed ‘Game Of Thrones' Buff Hasn't Even Finished Books Yet https://trib.al/y1NoZve\xa0pic.twitter.com/rvfNsGOEFy"
}]
```

## Conclusion

With the website content in a Python dictionary, we can now use it however we want. For example, we could return it as a JSON response for some other application, or convert it to a formatted HTML list with our custom styling. The opportunities are limited only by our imagination!

The final code looks like this:

```python
import requests
from bs4 import BeautifulSoup

if __name__ == '__main__':

        all_tweets = []

        url = 'https://twitter.com/TheOnion'

        data = requests.get(url)

        html = BeautifulSoup(data.text, 'html.parser')

        timeline = html.select('#timeline li.stream-item')

        for tweet in timeline:

                tweet_id = tweet['data-item-id']
                tweet_text = tweet.select('p.tweet-text')[0].get_text()

                all_tweets.append({"id": tweet_id, "text": tweet_text})

        print(all_tweets)
```

You can save the code above in a file called `scrape.py`, and then run it using:

```
python3 scrape.py
```

And that's all it takes. In 23 lines of code we've built a web scraper in Python. So please feel free to go ahead and see what you can do with your favourite website.

Have fun, and keep coding :)