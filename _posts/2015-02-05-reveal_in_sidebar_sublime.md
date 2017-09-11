---
layout: post
title: Reveal in sidebar hotkey for Sublime Text
---

Not sure why this one isn't bound by default, it is probably one of my most frequently used hotkeys in sublime.  If you right click on any file, you'll get a nice popup that says _Reveal in Sidebar_.  

![Sublime Text Popup Menu](http://i.imgur.com/gfAO9U4.png)

This will navigate the sublime folder bar to the file that you're currently working on.  I bound this bad boy to
<kbd>cmd</kbd> + <kbd>shift</kbd> + <kbd>r</kbd>.  Let me tell you.  *Life. Changing.*  You too can do this by updating your keybindings with this line:


```json
[
  {"keys": ["super+shift+r"], "command": "reveal_in_side_bar"}
]
```
