---
title: State Manager
description: Adding a State Manager to PyQuest.
date: 2026-05-11 22:29:00 +0530
categories: [Programming, General, DevBlog]
tags: [programming, devblog]
---

## State Manager

If one searches for the `State Machine` in programming, one would find a definition as follows:
```
A state machine (or finite state machine / FSM) is a computational model where a system exists in one of several defined states, and changes from one state to another based on inputs, events, or conditions.
```
In layman's terms, one could say that the program which implements a State Machine will be able to remain in a particular state at any single point of time.
It cannot be in 2 states at the same time. This also means that, states could be transitioned. The program may initially start in one state, but based on the 
conditions and situations, it will be transitioning from one state to another.

If we were to draw an analogy, we could say that a child's mind works in one way and an adult's mind works in another way. All adults have transitioned from the 
state of a child to that of an adult(This is a broad example, there are a lot of factors when it comes to analyzing people based on mindset, however, on broader terms
one could draw an analogy as such).

## Requirement of a state machine

All the games that I have played till date seemed to follow a certain pattern:

- The first thing that all the games showed was the name of the Publisher as well as the Game development studio apart from the name of the game.
- Post that, some of the games showed the name of the engine being used in the game or sometimes the tools used (Autodesk Scaleform, Nvidia Physx etc.)
- After showing these details, the title screen would be shown. The user would be prompted to press a key and the menu options would be shown or the menu options
would be shown directly.

The menu options is where the I think the first state stops and waits for the user to select what they would like to launch. Usually if the game has not been 
played earlier, it shows "Start a new game", "options" etc. If the game has already been played and there is a save file present in the location where save
files are supposed to be present, then the "Resume"/"Continue" option is shown when the menu options are shown.

The next state is loaded when the player selects to either start a new game or continue from where they might have left off. If the player chooses to pause the
game, typically done by pressing the `Escape` key, the menu state is loaded again and now the "Resume" option is added to the list of options.

As we can see, there is a requirement of a state machine in order to show the right options and handle inputs accordingly.

Implementation of a state machine would allow me to handle and organize certain things properly:
- Proper segregation of the event handling per state. This would allow me to use the same keys for different purposes in multiple states.
- Try to come closer to how the existing games in the market showcase their games using animations and other images/logos.
- Organize code in proper States as well as load resources for specific states in specific format.
- Try to load separate music for each state.

## State Machine

The state machine that I am implementing is having the following structure:

```python
class State:
    def __init__(self, ...):
        self.world = GameWorld()
        ...

class MenuState(State):
    def __init__(self, ...):
        ...

    def update(self, screen, events):
        ...

class GameState(State):
    def __init__(self, ...):
        ...

    def update(self, screen, events):
        ...
```

As you can see that, all the states that I am creating are taking the details from a common base class called State(which will be having certain details). As of now, I 
have not yet thought of the members of `State` or that of the other derived states, however, all of the states need to be having a common base class which will be 
containing the common game world. This is because, the game world will be populated when the player chooses the option to load a save or continue(which loads the last
save data) as well as when they select to play a new game. When the transition happens back to the Menu state and the user chooses to create a manual save, menu state will not be having all the necessary information of the game world, if the game world is present as a member of the Game State only.

Hence, it would be better to have a common game world whose details could be re-used and saved by the Menu State and re-used by the Game State.

## State Manager

In order for the states to be loaded and appropriate `update()` to be called, there has to be a manager for the states. For this reason, I created a `StateManager` which would be having the capacity to add states to it's storage, retrieve already created states or create new states in case no instance or prervious instance is destroyed.

The State manager is the one who would be handling which state is the current state and calling the appropriate `update` function in the main game loop.

After implementing the basic state manager, the logs are showing me the following output when I am moving from menu state to game state and vice versa.

```bash
pygame 2.1.3 (SDL 2.0.22, Python 3.9.6)
Hello from the pygame community. https://www.pygame.org/contribute.html
2026-05-13 02:09:02,279 : <MainThread  >(INFO ) -: launcher.py:23 - run() :- Verbose mode enabled.
Logging to file: logs/PyQuest.log
2026-05-13 02:09:02,279 : <MainThread  >(INFO ) -: launcher.py:31 - run() :- No configuration file provided. Using default settings.
2026-05-13 02:09:02,280 : <MainThread  >(DEBUG) -: launcher.py:33 - run() :- Default loaded configuration: {'display_config': <utility.config.DisplayConfig object at 0x107d978e0>, 'resource_config': <utility.config.ResoureConfig object at 0x107d97910>}
2026-05-13 02:09:02,280 : <MainThread  >(DEBUG) -: display.py:22 - __init__() :- Initializing PyQuestEngine...
2026-05-13 02:09:02,280 : <MainThread  >(INFO ) -: display.py:118 - run() :- Running PyQuestEngine...
2026-05-13 02:09:02,280 : <MainThread  >(INFO ) -: display.py:47 - initialize_display() :- Initializing display...
2026-05-13 02:09:02,973 : <MainThread  >(DEBUG) -: display.py:55 - initialize_display() :- Setting display mode to 800x600...
2026-05-13 02:09:03,392 : <MainThread  >(DEBUG) -: display.py:63 - initialize_display() :- Setting window title to '"PyQuest"'...
2026-05-13 02:09:03,394 : <MainThread  >(DEBUG) -: display.py:37 - setup_states() :- Setting up game states...
2026-05-13 02:09:03,394 : <MainThread  >(DEBUG) -: states.py:23 - __init__() :- MenuState initialized
2026-05-13 02:09:03,410 : <MainThread  >(DEBUG) -: states.py:126 - __init__() :- GameState initialized
2026-05-13 02:09:03,411 : <MainThread  >(INFO ) -: display.py:68 - load_resources() :- Loading resources...
2026-05-13 02:09:03,412 : <MainThread  >(DEBUG) -: display.py:128 - run() :- Entering main loop...
2026-05-13 02:09:05,379 : <MainThread  >(DEBUG) -: states.py:89 - handle_input() :- Selected menu option: Start Game
2026-05-13 02:09:05,379 : <MainThread  >(DEBUG) -: states.py:94 - process_options() :- Processing menu option: Start Game
2026-05-13 02:09:05,379 : <MainThread  >(DEBUG) -: states.py:96 - process_options() :- Starting new game...
2026-05-13 02:09:05,379 : <MainThread  >(DEBUG) -: display.py:106 - update() :- Changing state to game...
2026-05-13 02:09:06,450 : <MainThread  >(DEBUG) -: states.py:152 - update() :- Escape key pressed. Transitioning back to menu...
2026-05-13 02:09:06,450 : <MainThread  >(DEBUG) -: display.py:106 - update() :- Changing state to menu...
2026-05-13 02:09:07,668 : <MainThread  >(DEBUG) -: states.py:89 - handle_input() :- Selected menu option: Exit
2026-05-13 02:09:07,668 : <MainThread  >(DEBUG) -: states.py:94 - process_options() :- Processing menu option: Exit
2026-05-13 02:09:07,669 : <MainThread  >(WARNI) -: states.py:111 - process_options() :- Exiting game...
```

As one can see, I transitioned from game state when the start game option was selected. I transitioned back from the Game State to the Menu State
when the player pressed the `Escape` key.

## Next Steps

The next steps for me is to load the story data and show the text on the screen. It might take some time before I post again since it will take some
time in order to understand and implement the working of a text based adventure game.