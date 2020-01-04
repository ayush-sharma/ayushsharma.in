---
layout: post
title:  "NANO Keyboard Shortcuts"
number: 4
date:   2016-08-12 1:00
categories: ninja
---
Keyboard shortcuts are designed to ease your life so you don't have to remember a lot of shit, but I can never seem to remember them. I guess I like typing a lot more than I care to admit. But I do need to be more productive, so I sort-of-commit today to learn at least a few keyboard shortcuts every day. I'll start with Nano since its nano (get it?). I've divided up the shortcuts into categories so I can recall them more easily.

But first, a little history. Nano started life as a free replacement for the Pico text editor. And that's about it, really. It's pretty simple to use and powerful-enough that you should be able to do pretty much anything, but for more power and control use Vi.

So here we go. Control-key sequences are notated with a caret (^) symbol and can be entered either by using the Control (Ctrl) key or pressing the Escape (Esc) key twice.  Escape-key sequences are notated with the Meta (M-) symbol and can be entered using either the Esc, Alt, or Meta key depending on your keyboard setup. Alternative keys are shown in parentheses.

## Help Commands
```
help
^G      (F1)            Display this help text
```

## File Commands
```
^O      (F3)            Write the current file to disk
^X      (F2)            Close the current file buffer / Exit from Nano
^R      (F5)            Insert another file into the current one
M-<             (M-,)   Switch to the previous file buffer
M->             (M-.)   Switch to the next file buffer
M-D                     Count the number of words, lines, and characters
^L                      Refresh (redraw) the current screen
```

## Edit Commands
```
M-^             (M-6)   Copy the current line and store it in the cutbuffer
^K      (F9)            Cut the current line and store it in the cut buffer
^U      (F10)           Uncut from the cutbuffer into the current line
M-V                     Insert the next keystroke verbatim

^I                      Insert a tab at the cursor position

^M                      Insert a newline at the cursor position

^D                      Delete the character under the cursor

^H                      Delete the character to the left of the cursor
M-T                     Cut from the cursor position to the end of the file
^W      (F6)            Search for a string or a regular expression
        (F16)   (M-W)   Repeat last search
^T      (F12)           Invoke the spell checker, if available
^\      (F14)   (M-R)   Replace a string or a regular expression
```

## Formatting Commands
```
^J      (F4)            Justify the current paragraph
M-J                     Justify the entire file
```

## Navigation Commands
```
^Y      (F7)            Move to the previous screen
^V      (F8)            Move to the next screen
^_      (F13)   (M-G)   Go to line and column number
^^      (F15)   (M-A)   Mark text at the cursor position
^C      (F11)           Display the position of the cursor
^A                      Move to the beginning of the current line

^E                      Move to the end of the current line

M-(             (M-9)   Move to the beginning of the current paragraph

M-)             (M-0)   Move to the end of the current paragraph

M-\             (M-|)   Move to the first line of the file
M-/             (M-?)   Move to the last line of the file
M-]                     Move to the matching bracket

M--             (M-_)   Scroll up one line without scrolling the cursor
M-+             (M-=)   Scroll down one line without scrolling the cursor
```

## Enable/Disable Settings
```
M-X                     Help mode enable/disable
M-C                     Constant cursor position display enable/disable
M-O                     Use of one more line for editing enable/disable
M-S                     Smooth scrolling enable/disable
M-P                     Whitespace display enable/disable
M-Y                     Colour syntax highlighting enable/disable
M-H                     Smart home key enable/disable

M-I                     Auto indent enable/disable

M-K                     Cut to end enable/disable

M-L                     Long line wrapping enable/disable
M-Q                     Conversion of typed tabs to spaces enable/disable
M-B                     Backup files enable/disable

M-F                     Multiple file buffers enable/disable

M-M                     Mouse support enable/disable

M-N                     No conversion from DOS/Mac format enable/disable
M-Z                     Suspension enable/disable
```