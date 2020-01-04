---
layout: post
title:  "Essential VIM Keyboard Shortcuts"
number: 10
date:   2016-08-15 0:00
categories: ninja
---
Out of the 100 or so keyboard shortcuts available for Vim, I'll mention the basic ones here:

## Main
```
Esc - Command mode; Shortcuts listed below work in this mode.
i - Insert mode; Start typing your content now.
: - Commands for saving document, quitting, etc. go here.
. - Repeat last command
```

## Navigation
```
w - Jump forward to start of word.
e - Jump forward to end of word.
0 - Jump to start of line.
$ - Jump to end of line.
^ - Jump to first non-blank character of line.
g_ - Jump to last non-black character of line.
5w - Move forward 5 words.
5b - Move backward 5 words.
gg - Go to first line of document.
G - Go to last line of document.
10G - Go to line 10.
:10 - Go to line 10.
} - Jump to next paragraph.
{ - Jump to previous paragraph.
```

## Inserting
```
i - Insert before cursor.
a - Insert after cursor.
o - Append new line below the current line.
O - Append new line above the current line.
```

## Cut, Copy, and Paste
```
dd - Cut a line.
d$ - Cut till the end of line.
x - Cut character.
yy - Copy current line.
y$ - Copy till end of line.
p - Paste after cursor.
P - Paste before cursor.
```

## Deleting
```
x - Delete character at cursor.
dw - Delete a word.
d0 - Delete till beginning of line.
d$ - Delete till end of line.
dgg - Delete till beginning of file.
dG - Delete till end of file.
```

## Undo/Redo
```
u - Undo.
Ctrl + r - Redo.
```

## Search and Replace
```
/pattern - Search for pattern.
?pattern - Search backward for a pattern.
n - Repeat search in same direction.
N - Repeat search in opposite direction.
:%s/old/new/g - Replace all old with new throughout file.
:%s/old/new/gc - Replace all old with new throughout file with confirmations.
```

## Saving and Quitting
```
:w - Save document.
:q - Quit document.
:wq - Save and quit.
:q! - Quit without saving changes.
```