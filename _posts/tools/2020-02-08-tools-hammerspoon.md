---
layout: post
title: 'Tools - Hammerspoon'
summary: 'A brief overview of using Hammerspoon for Window Management'
category: 'tools'
---

# The Problem

Resizing windows with a mouse is annoying.

# The Solution

Use the automation tool [Hammerspoon](https://www.hammerspoon.org/)

# Lua

Hammerspoon scripts are written in [Lua](https://www.lua.org/)

[Learn X in Y Minutes Where X=Lua](https://learnxinyminutes.com/docs/lua/)

# Window Grid

In `.hammerspoon/init.lua`

```lua
-- config reload
hs.alert.show("Config loaded")

-- animation duration
hs.window.animationDuration = 0

-- grid config
hs.grid.GRIDHEIGHT = 4
hs.grid.GRIDWIDTH = 4
hs.grid.MARGINX = 0
hs.grid.MARGINY = 0

-- manual window move screen / resize
hs.hotkey.bind({"ctrl"}, "D", function()
  hs.grid.show()
end)
```

## Usage

1. `CTRL + D` to display the grid
1. Letter of the grid that corresponds with upper left corner of the window
1. Letter of the grid that corresponds with lower right corner of the window

e.g.

+ Full screen: `CTRL+D 1 V`
+ Left half of screen: `CTRL+D 1 X`
+ Right half of screen: `CTRL+D 3 V`
