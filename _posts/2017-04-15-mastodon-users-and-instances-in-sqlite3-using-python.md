---
layout: post
title:  "Mastodon Users and Instances in Sqlite3 Using Python"
number: 40
date:   2017-04-15 0:00
categories: development
---
I made a script to get the current number of users and instances of the new Mastodon social network. The script will download the information from the handy `json` file these great guys maintain, and then add the information to a local `Sqlite3` database. This is for `Python 3`. Have a look.

```python
import json
import locale
import sqlite3
import time

import requests


def create_db():
    """ Check if DB exists and the tables are there. Create it if not found. """
    database_file = 'data.db'    
    connection = sqlite3.connect(database=database_file)
    cursor = connection.cursor()

    cursor.execute('CREATE TABLE IF NOT EXISTS mastodon_users (`users` INTEGER UNSIGNED, `instances` INTEGER UNSIGNED, `timestamp` INTEGER UNSIGNED)')

    connection.commit()
    connection.close()


def insert_values_in_db(user_count, server_count):
    """ Insert user and instance count values in database """
    database_file = 'data.db'
    connection = sqlite3.connect(database=database_file)
    cursor = connection.cursor()

    cursor.execute('INSERT INTO mastodon_users VALUES (?, ?, ?)', [user_count, server_count, int(time.time())])

    connection.commit()
    connection.close()


locale.setlocale(locale.LC_ALL, 'en_US')

link = 'https://instances.mastodon.xyz/instances.json'r = requests.get(link)
rjson = json.loads(r.text)

users = 0
servers = 0
for item in rjson:
    users = users + item['users']

    servers = servers + 1
users_formatted = locale.format("%d", users, grouping=True)
servers_formatted = locale.format("%d", servers, grouping=True)

print("%s users on %s servers." % (users_formatted, servers_formatted))

create_db()
insert_values_in_db(user_count=users, server_count=servers)
```