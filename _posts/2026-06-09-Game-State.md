---
title: Game State
description: Game State and associated work as of now.
date: 2026-06-09 07:07:11 +0530
categories: [Design, General, DevBlog]
tags: [design, devblog]
---

## Game State

My progress with the PyQuest text based engine has been slow. As a matter of fact, it has been extremely slow. This might be because I
had participated in a Hackathon in office. Coupled with the normal work that I do in office, plus the pressure of the last minute
changes that I needed to do for the Hackathon, I was not able to sit down for any more work on the engine.

From yesterday, I have been able to sit down and start working on the engine once again. Going through the existing code, I could see
that I was working on the Game State. As of now, the state transitions properly from Menu state to Game state. When Game state is selected
since none of the resources are being shown on the screen, the screen appears blank, completely dark.

Here there are two things to be noted:
 - Story
 - Music

I will be working on the story for now, music is something that I will try to take care later on.


### Story

For the story, the main requirement is to allow the user to show the text in a scrollable fashion. The engine user should be able to specify
the file which will be containing the story and the engine should do the heavy lifting of showing the text on the screen in the proper manner.
What would be the proper manner? I am not too sure about how I can put it but here goes - the story text should not be shown for the entirety
of the screen. It should be shown for a certain portion of the screen and the other remaining part of the screen should be able to show actions
and controls such that the player does not feel a wall of text is thrust on their face.

The other thing for showing the story is that, it should allow the player to scroll through the text, allowing to go up and down the text and
take notes if required. I have already experimented on this part by writing a test bed code where I have scrolled through text displayed on the
screen. There were a few kinks, one of them being able to go down on the screen/go above the text that has been written. This causes the player
to see blank sections which I am not in favour of showing. On the contrary, I have a different design in mind.


### Story Display


The design that I have in mind is, the text is shown as of being typed. If the player presses enter key, the text gets written completely, disabling
the typing animation. Once the entire line of text is printed (blitted) on the screen, the player will be able to choose from a list of actions for
that stage and move on to the next stage. Here, by stage, I am referring to each scenario. A level in such a case could be composed of many such
scenarios.


## Line length and blitting issue

There is one problem with the text surfaces approach that I feel I will have to take care of. The test bed code that I wrote, I had a simple line
where-in I did not have to bother about the length of the line, since the line length was always less (rather much less) than the total width of the
screen. However, in this case, the engine user could be writing long of lines of text in the stage (scene) file. While showing the text on the screen
I need to make sure that the length of the line being shown as well as the font size is taken into consideration to match the size (height and width)
of the screen. This is something that might require some careful and attentive implementation phase - since the screen size will also be dynamic
depending on how the engine user was running and how the player would be running it.

Going fullscreen should also be something that needs to be looked at, as to how that could be controlled.

### A side note on configuration

I am trying to make the engine as much configurable as possible such that the engine user would be able to develop in a windowed mode by default and
then ship their zip file with a simple configuration option of making the engine to go fullscreen. I think, there should also be a capacity to directly
read and load resources from a zip file without actually opening or extracting the contents of the zip file. This way the engine user could share a game
created with others without having any issues of publishing, signing and the like.

The ideas are fun, I can only implement them once I am done with the story display for the Game state.
