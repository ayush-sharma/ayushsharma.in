---
layout: post
title:  "Fixing ValueError('unknown locale: %s' % localename) in Python"
number: 38
date:   2017-03-30 0:00
categories: development
---
If you're getting `raise ValueError('unknown locale: %s' % localename)` when executing your Python script, there is a simple fix. Before executing your script, run the following:

```bash
export LC_ALL=en_US.UTF-8
export LANG=en_US.UTF-8
```

It might make your life easier just to add it to your `~/.bash_profile` file.