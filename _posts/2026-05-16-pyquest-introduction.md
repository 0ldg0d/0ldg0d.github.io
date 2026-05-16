---
title: PyQuest Introduction
description: Showing the introduction of the story using PyQuest.
date: 2026-05-16 20:20:21 +0530
categories: [Programming, General, DevBlog]
tags: [programming, devblog]
---

## Story Mode Requirements

The engine is now in a position where I could start working on the story to be shown. Since this engine will support for text based adventure, the main thing that this engine should support is finding out the length of the text to be shown and then making
sure that the text is properly shown in the screen. The requirements could be listed as follows:

- `Show text on the screen based on the size of the screen and length of the text`:

    After loading the text from the respective resource file, the engine should be able to figure out the amount of text that could be shown on the screen and should
    automatically add the respective controls in order to show the text.

- `Allow proper controls to show the text on screen, such that player should be able to scroll between text shown`:

    The text shown on the screen should allow the player to scroll for text which spans more than the length of the screen(or surface). This should allow the player
    to read the text at their own pace. This is very much required since this is integral for a text based adventure.

- `Integrate actions for scenarios`

    The engine should also allow for showing actions that could be taken by the player in a proper format. This might be taking some more space in the screen if we are 
    doing something like the old-school UI. The UI needs to be thought about more and designed properly.

- `Support for controllers`

    The engine should automatically detect controller(if any are attached) and then allow the players to use the controller. The support should be for Steam Deck and 
    normal controller. Will not be supporting any XBox controllers(primarily because I donot own one anymore on account of the expensive pricing). Will try to see if I could get the DualSense to work as well.


## Progress so far...

As of now, I have just written a test bed code, which allows the player to scroll through the text being shown. This needs to be ported to the main game engine in 
such a way that the developer does not have to worry about the screen size of the text size and all. Everything should be taken care automatically by the engine
code. Have to start working on this one.

The night is young, the system is fully charged and I should be able to progress at least a bit, hoping for the progress now...