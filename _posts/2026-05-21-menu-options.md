---
title: Menu Options
description: Menu Options for the game.
date: 2026-05-21 20:18:24 +0530 
categories: [Programming, General, DevBlog]
tags: [programming, devblog]
---

## Menu Options

Whenever we open a game, the state that we land in is the Menu State. The menu state contains the option which 
allows the player to choose from various actions that they could do. The player can start a new game, load an 
already saved game, select the options/settings and change various settings to be used for the game or exit the 
game and return to the window manager.

Each of these options causes a transition of state. The major switch happens from `Menu State` to `Game State` and 
vice-versa. Apart from that, there would be the ocassional `Options State` which would be opened any time the player 
intends to update certain settings of the game.

## Menu options added being dynamically

When the player is playing the game, sometimes they press the `Escape` key in order to either pause the game or reload 
a certain save game in order to reverse some decision which they might have made in the game. In such a case, the menu 
options should also include the _"Resume Game"_ and _"Save Game"_. 

These options need to be shown in case the player has started the game and pressed the `Escape` key.

## Showing Menu Options

In the `Menu State` we have to take care of 2 scenarios:
- **Initial Menu options**: The initial menu options will be containing the options for starting a new game, loading 
a previously saved game, options as well as the exit option.
- **On pressing the `Escape` key**: When the `Escape` key is pressed after starting the game, the player should be 
able to see 2 more options in the main list of options in the Menu State. Those would be to resume the game as well as
to save the game.


With these 2 scenarios in mind, as well as the state transition, the first thing that we would require is to have a 
base `State` class from which all the states will be inheriting. It would look something as follows:

```python
class State:
    def __init__(self, name: str, bus: Bus):
        self.name = name
        self.world = GameWorld()
        self.bus = bus
        ...

    def update(self, *args, **kwargs) -> Union[None, str]:
        raise NotImplementedError("The 'update' method must be implemented by the subclasses of State")
```

Here in the base class, we have added a variable `name` which stores the name of the class to be used, the `world` which will be
loading all the game related resources. This is added so that the game could be saved with the player details. The player would 
be present in the game world. In order to get the details from the game world, it would be better to have a common game world to
poke at and find details of the various data, player position, inventory etc.

Now, when the states are implemented, the `update` function needs to be implemented inside them.

Apart from the world, there is also the bus which we could use in order to communicate between the states as well as inform the 
state manager to perform operations(especially state transition).

### Menu State Implementation

In the menu state, we need to implement the `update` function wherein we would be calling the function to show the `show_menu`. 
This is a special function of the menu state. The `update` function would look something as follows:

```python
class MenuState(State):
    ...
    def update(self, screen: pygame.Surface, events: list) -> None:
        self.show_menu(screen=screen)
        self.handle_input(events=events)
    ...
```

**NOTE**: We need state specific event handling since each state would have different actions that the player is allowed to perform.

The `show_menu` function would be responsible for showing the options(either the initial options or the updated options). We have to make sure that the 
function does not have any logging since the `show_menu` function will be called inside the `update` function and the update function for each state 
will be called in the display main loop.

So the `show_menu` function could look like something as follows:

```python
    def show_menu(self, screen: pygame.Surface) -> None:
        if self.bus._is_in_game and "Resume Game" not in set(self.options) and "Save Game" not in set(self.options): # type: ignore
            self.options.insert(0, "Resume Game")
            self.options.insert(1, "Save Game")

        if screen:
            screen.fill((0, 0, 0))  # Clear the screen with a black background
            for opt_index, option in enumerate(self.options):
                if not self.font:
                    error("Font not set, please check")
                    raise Exception("Font not set, please check")
                text_surface = None
                if opt_index == self.current_selection:
                    text_surface = self.font.render(
                        f"{option}", True, (0, 255, 0))  # Render the selected option as green text
                else:
                    text_surface = self.font.render(
                        option, True, (255, 255, 255))  # Render the menu option as white text

                if text_surface:
                    screen.blit(text_surface, (
                        50, 50 + self.options.index(option) * 30))  # Draw the text surface onto the screen
```

Here we are checking if the player has already gone inside the game(as in switched to the game state). If the player has been in game, it means that
on pressing the `Escape` key, the options should be updated. Post that it is the standard thing of `blit`-ing on the surface with the necessary options
as well as showing a color of text for the selected option.


## Log output

Looking at the logs, the following kind of logs is what is present for debugging purpose at the moment:

```bash
 2026-05-22 02:45:48,772   MainThread  󰿨 INFO   launcher.py:31  N/A  run()  No configuration file provided. Using default settings.
 2026-05-22 02:45:48,774   MainThread  󰿨 DEBUG  launcher.py:33  N/A  run()  Default loaded configuration: {'display_config': <utility.config.DisplayConfig object at 0x1079a8b80>, 'resource_config': <utility.config.ResourceConfig object at 0x1079a8bb0>}
 2026-05-22 02:45:48,774   MainThread  󰿨 DEBUG  display.py:22  PyQuestEngine  __init__()  Initializing PyQuestEngine...
 2026-05-22 02:45:48,775   MainThread  󰿨 INFO   display.py:120  PyQuestEngine  run()  Running PyQuestEngine...
 2026-05-22 02:45:48,776   MainThread  󰿨 INFO   display.py:46  PyQuestEngine  initialize_display()  Initializing display...
 2026-05-22 02:45:49,605   MainThread  󰿨 DEBUG  display.py:54  PyQuestEngine  initialize_display()  Setting display mode to 800x600...
 2026-05-22 02:45:50,101   MainThread  󰿨 DEBUG  display.py:62  PyQuestEngine  initialize_display()  Setting window title to '"PyQuest"'...
 2026-05-22 02:45:50,103   MainThread  󰿨 DEBUG  display.py:36  PyQuestEngine  setup_states()  Setting up game states...
 2026-05-22 02:45:50,105   MainThread  󰿨 DEBUG  states.py:26  MenuState  __init__()  MenuState initialized
 2026-05-22 02:45:50,106   MainThread  󰿨 DEBUG  states.py:47  MenuState  load_menu_resources()  Loading game resources from resources/story.json...
 2026-05-22 02:45:50,108   MainThread  󰿨 DEBUG  states.py:56  MenuState  load_menu_resources()  Successfully loaded game resources from resources/story.json -> Data: {'start': 'introduction', 'menu': {'root': 'resources', 'directory': 'menu', 'font': 'mfont.ttf', 'font_size': 18, 'bg_music': 'menu_music.mp3'}, 'introduction': {'root': 'resources', 'directory': 'intro', 'file': 'introduction.txt', 'font': 'Arial', 'bg_music': 'intro_music.mp3'}}
 2026-05-22 02:45:50,109   MainThread  󰿨 DEBUG  states.py:59  MenuState  load_menu_resources()  Extracted menu-specific resource data: {'root': 'resources', 'directory': 'menu', 'font': 'mfont.ttf', 'font_size': 18, 'bg_music': 'menu_music.mp3'}
 2026-05-22 02:45:50,111   MainThread  󰿨 DEBUG  states.py:63  MenuState  load_menu_resources()  Menu resource loaded: namespace(root='resources', directory='menu', font='mfont.ttf', font_size=18, bg_music='menu_music.mp3')
 2026-05-22 02:45:50,112   MainThread  󰿨 DEBUG  states.py:70  MenuState  load_menu_resources()  Menu resource directory 'resources/menu' found. Loading menu resources...
 2026-05-22 02:45:50,113   MainThread  󰿨 DEBUG  states.py:78  MenuState  load_menu_resources()  Menu font file 'resources/menu/mfont.ttf' found. Loading custom font...
 2026-05-22 02:45:50,114   MainThread  󰿨 DEBUG  states.py:83  MenuState  load_menu_resources()  Menu font size specified in the resource file: 18.
 2026-05-22 02:45:50,119   MainThread  󰿨 DEBUG  states.py:161  GameState  __init__()  GameState initialized
 2026-05-22 02:45:50,120   MainThread  󰿨 DEBUG  states.py:170  GameState  load_game_resources()  Loading game resources from resources/story.json...
 2026-05-22 02:45:50,121   MainThread  󰿨 DEBUG  states.py:180  GameState  load_game_resources()  Successfully loaded game resources from resources/story.json -> Data: {'start': 'introduction', 'menu': {'root': 'resources', 'directory': 'menu', 'font': 'mfont.ttf', 'font_size': 18, 'bg_music': 'menu_music.mp3'}, 'introduction': {'root': 'resources', 'directory': 'intro', 'file': 'introduction.txt', 'font': 'Arial', 'bg_music': 'intro_music.mp3'}}
 2026-05-22 02:45:50,121   MainThread  󰿨 INFO   display.py:67  PyQuestEngine  load_resources()  Loading resources...
 2026-05-22 02:45:50,123   MainThread  󰿨 DEBUG  display.py:130  PyQuestEngine  run()  Entering main loop...
 2026-05-22 02:45:52,030   MainThread  󰿨 DEBUG  states.py:125  MenuState  handle_input()  Selected menu option: Start Game
 2026-05-22 02:45:52,031   MainThread  󰿨 DEBUG  states.py:130  MenuState  process_options()  Processing menu option: Start Game
 2026-05-22 02:45:52,031   MainThread  󰿨 DEBUG  states.py:132  MenuState  process_options()  Starting new game...
 2026-05-22 02:45:52,032   MainThread  󰿨 DEBUG  display.py:105  PyQuestEngine  update()  Changing state to game...
 2026-05-22 02:45:52,032   MainThread  󰿨 DEBUG  display.py:109  PyQuestEngine  update()  Transitioning to GAME state. Loading game resources...
 2026-05-22 02:45:53,373   MainThread  󰿨 DEBUG  states.py:197  GameState  handle_input()  Escape key pressed. Transitioning back to menu...
 2026-05-22 02:45:53,376   MainThread  󰿨 DEBUG  display.py:105  PyQuestEngine  update()  Changing state to menu...
 2026-05-22 02:45:54,799   MainThread  󰿨 DEBUG  states.py:125  MenuState  handle_input()  Selected menu option: Exit
 2026-05-22 02:45:54,800   MainThread  󰿨 DEBUG  states.py:130  MenuState  process_options()  Processing menu option: Exit
 2026-05-22 02:45:54,801   MainThread  󰿨 WARNI  states.py:146  MenuState  process_options()  Exiting game...
```