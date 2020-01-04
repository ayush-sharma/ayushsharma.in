---
layout: post
title:  "Introduction to YAML"
number: 14
date:   2016-08-20 0:00
categories: development
---
YAML (YAML Ain't Markup Language) is a human-readable markup language. Its syntax was designed to be easily read by humans, and does not contain quotation marks, open or close tag, brackets or braces, which might make it harder for humans to parse nesting rules. You can simply scan your YAML document and immediately know what’s going on.

## Design Goals
- It is easily read by humans.
- It is easily portable between programming languages.
- It matches native data structures of Agile languages.
- It has a consistent model to support generic tools.
- It supports one-pass processing.
- It is expressive and extensible.
- It is easy to implement and use.

Let's start with some basics.

Can you figure out what's going on below?

```yaml
-------
# My grocery list
groceries:
     - Milk
     - Eggs
     - Bread
     - Butter
...
```

The above example contains a simple list of groceries to buy, and it’s a fully formed YAML document. As you can see, strings don't need to be quoted, lists values are denoted with simple hyphen+space, and there are no opening or closing tags. A YAML document starts with `---` and ends with `...`, but you can actually skip those depending on where you're using YAML, and whether or not you plan on defining multiple YAML documents in the same file. Comments in YAML start with a #.

Indentation is key in YAML. Indentation is only allowed with spaces and not tabs, but the specific number of spaces are not important as long as items on the same level have the same number of spaces, and items lower in the nesting order contain more spaces than contained in the level above.

## Basis Elements

### Collections
YAML can have two types of collections: lists (for sequences) and dictionaries (for mappings). Lists are key-value pairs where every value is on a new line, beginning with a hyphen+space. Dictionaries are key-value pairs where every value is a mapping contain a key, a colon+space, and a value. For example:

```yaml
# My List
groceries:
     - Milk
     - Eggs
     - Bread
     - Butter

# My dictionary
contact:
     name: Ayush Sharma
     email: hi@ayushsharma.in
```
Lists and dictionaries can be combined to contain more complex data structures. Lists can contain dictionaries, and dictionaries can contain lists.

### Strings
Strings in YAML do not require quotation marks. Multi-line strings can be defined  using | or >. The former preserves newlines, the latter does not. For example:

```yaml
my_string: |
     This is my string.
     It can contain multiple lines.
     Newlines will be preserved.
```

```yaml
my_string_2: >
     This is my string.
     This can also contain multiple lines.
     Newlines will not be preserved, and all of these lines will be folded.
```

### Anchors
YAML can have repeatable blocks of data using node anchors. The "&" character defines a block of data that can be referred to later using the "*" character. For example:

```yaml
billing_address: &add1
     house: B1
     street: My Street

shipping_address: *add1
```

At this point, you know enough YAML to get started. You can play around with the online YAML parser to test yourself. If you're going to be doing more complex YAML, like building a parser, you should go through the official documentation, or check out the official website for a list of parsers in other languages.

## Resources
- [Official YAML website](http://yaml.org/)
- [YAML Cheat Sheet](http://yaml.org/refcard.html)
- [Online YAML parser to play around with](https://yaml-online-parser.appspot.com/)