---
title: Enhancing Logging Format
description: Enhancing the logging format of PyQuest Engine.
date: 2026-05-17 01:27:42 +0530
categories: [Programming, General, DevBlog]
tags: [programming, devblog]
---

## Requirement of the enhancement

My logging needed a little enhancement. Given the format that I am using at the moment in order to log my messages, I am not able to find out quite some
amount of information in my logs. 
For example, the current logging does not document the class name of the function that is being run. It does show the name of the file, the line number
as well as the name of the function, however there is an issue.

When overridden functions are being called, it is not easy to discern in a State Machine, what is the class whose member function is being called. For example, 
take a look at the logs below:

```bash
(python) ➜  pyquest ./launcher.py -v
pygame 2.1.3 (SDL 2.0.22, Python 3.9.6)
Hello from the pygame community. https://www.pygame.org/contribute.html
2026-05-13 02:51:07,274 : <MainThread  >(INFO ) -: launcher.py:23 - run() :- Verbose mode enabled.
Logging to file: logs/PyQuest.log
2026-05-13 02:51:07,274 : <MainThread  >(INFO ) -: launcher.py:31 - run() :- No configuration file provided. Using default settings.
2026-05-13 02:51:07,275 : <MainThread  >(DEBUG) -: launcher.py:33 - run() :- Default loaded configuration: {'display_config': <utility.config.DisplayConfig object at 0x10c1a08e0>, 'resource_config': <util
ity.config.ResourceConfig object at 0x10c1a0910>}
2026-05-13 02:51:07,275 : <MainThread  >(DEBUG) -: display.py:22 - __init__() :- Initializing PyQuestEngine...
2026-05-13 02:51:07,275 : <MainThread  >(INFO ) -: display.py:117 - run() :- Running PyQuestEngine...
2026-05-13 02:51:07,275 : <MainThread  >(INFO ) -: display.py:46 - initialize_display() :- Initializing display...
2026-05-13 02:51:07,794 : <MainThread  >(DEBUG) -: display.py:54 - initialize_display() :- Setting display mode to 800x600...
2026-05-13 02:51:08,233 : <MainThread  >(DEBUG) -: display.py:62 - initialize_display() :- Setting window title to '"PyQuest"'...
2026-05-13 02:51:08,235 : <MainThread  >(DEBUG) -: display.py:36 - setup_states() :- Setting up game states...
2026-05-13 02:51:08,235 : <MainThread  >(DEBUG) -: states.py:24 - __init__() :- MenuState initialized
2026-05-13 02:51:08,252 : <MainThread  >(DEBUG) -: states.py:127 - __init__() :- GameState initialized
2026-05-13 02:51:08,252 : <MainThread  >(DEBUG) -: states.py:136 - load_game_resources() :- Loading game resources from resources/story.json...
2026-05-13 02:51:08,253 : <MainThread  >(DEBUG) -: states.py:145 - load_game_resources() :- Successfully loaded game resources from resources/story.json -> Data: {'start': 'introduction', 'introduction': {'file': 'introduction.txt', 'font': 'Arial', 'bg_music': 'intro_music.mp3'}}
2026-05-13 02:51:08,253 : <MainThread  >(INFO ) -: display.py:67 - load_resources() :- Loading resources...
2026-05-13 02:51:08,255 : <MainThread  >(DEBUG) -: display.py:127 - run() :- Entering main loop...
2026-05-13 02:51:09,870 : <MainThread  >(DEBUG) -: states.py:90 - handle_input() :- Selected menu option: Start Game
2026-05-13 02:51:09,870 : <MainThread  >(DEBUG) -: states.py:95 - process_options() :- Processing menu option: Start Game
2026-05-13 02:51:09,870 : <MainThread  >(DEBUG) -: states.py:97 - process_options() :- Starting new game...
2026-05-13 02:51:09,870 : <MainThread  >(DEBUG) -: display.py:105 - update() :- Changing state to game...
2026-05-13 02:51:10,824 : <MainThread  >(DEBUG) -: states.py:162 - handle_input() :- Escape key pressed. Transitioning back to menu...
2026-05-13 02:51:10,825 : <MainThread  >(DEBUG) -: display.py:105 - update() :- Changing state to menu...
2026-05-13 02:51:11,528 : <MainThread  >(DEBUG) -: states.py:90 - handle_input() :- Selected menu option: Exit
2026-05-13 02:51:11,528 : <MainThread  >(DEBUG) -: states.py:95 - process_options() :- Processing menu option: Exit
2026-05-13 02:51:11,528 : <MainThread  >(WARNI) -: states.py:112 - process_options() :- Exiting game..
```

As one can easily spot, `update()` and `handle_input()` are getting called, however I am not sure from which class. Also it is not fun if I 
have to embed the class name in the message and depend on that in order to debug using the logs. 

## What would be the solution?

The solution would be to show the name of the class along with the name of the function being called. If the function is not in a class, then it
should mention "N/A" or something of that sort. It would be better, if something like `%(classname)s` could be added the logger format such that 
the class name if already picked up.

## Issue with updating the logging format

I did update the format to log the classname using the `%(classname)s`, however that just threw a lot of errors. A careful check of the documentation
suggested to use a custom `Filter` from the logging module. This custom filter would allow for filtering out the name of the class and add it to the 
`record` wherein this variable could be referred in the logging format string.

## Custom filter

I wrote a basic custom filter - taking some of the suggestions from Google's AI mode. The code, after my own tweaking is as follows:

```python
class ClassNameFilter:
    """Custom logging filter to add the class name to log records."""

    def filter(self, record):
        """Add the class name to the log record."""
        record.classname = "N/A"

        for frame in stack():
            if 'logging' in frame.filename and '__init__' not in frame.filename:
                continue

            frame_self = frame.frame.f_locals.get('self', None)
            if frame_self:
                # record.classname = type(frame_self).__name__
                class_name = frame_self.__class__.__name__
                if (
                    "logging" in frame.filename or 
                    isinstance(frame_self, Filter) or 
                    isinstance(frame_self, Handler) or
                    class_name in ("StreamHandler", "FileHandler", "ClassNameFilter", "LogScribe")
                ):
                    continue
                
                # Capture the first valid business logic class name
                record.classname = class_name
                break
        return True
```
As could be seen, in the filter function, the aim of the filter is to check if the class name has anything from the logging module or 
from the custom Filter or Handler module. It also checks and filters out anything from the StreamHandler, FileHandler, ClassNameFilter as well as the
custom Logging class as well. 
If all these classes are removed, the only class name that should remain in the stack frame would be that of the actual class from which that function is 
called. Given now, the `classname` variable is added to the record, the same could be referred in the logging format string.

After writing the custom filter, the same needs to be added to all the handlers that are being added to `LogScribe`. The changes are as follows:

```python
class LogScribe:
    def __init__(self, ...):
        ...
        self._class_filter = ClassNameFilter()
        ...

    def enable_stream_logging(self, enable: bool):
        ...
        self._shandler_addFilter(self._class_filter)
        ...

    def enable_filelogging(self, enable: bool):
        ...
        self._fhandler.addFilter(self._class_filter)
        ...
```

Once these are added, the next part is to update the format string.

## Updating the format string

While trying to add the `%(classname)s`, suddenly I felt the urge to add some more unicode characters, just to make the log look a bit fun and easier to read.
I ended up updating the logging format string to the following format:

```python
DEFAULT_LOG_FMT = " %(asctime)s   %(threadName)-12.12s" +\
        "󰿨 %(levelname)-5.5s  %(filename)s:%(lineno)s  " +\
    "%(classname)s  %(funcName)s()  %(message)s"
```

## The final logging output

With the aforementioned changes, the final log looks as follows:

```bash
(python) ➜  pyquest ./launcher.py -v
pygame 2.1.3 (SDL 2.0.22, Python 3.9.6)
Hello from the pygame community. https://www.pygame.org/contribute.html
 2026-05-17 03:29:22,923   MainThread  󰿨 INFO   launcher.py:23  N/A  run()  Verbose mode enabled.
Logging to file: logs/PyQuest.log
 2026-05-17 03:29:22,938   MainThread  󰿨 INFO   launcher.py:31  N/A  run()  No configuration file provided. Using default settings.
 2026-05-17 03:29:22,939   MainThread  󰿨 DEBUG  launcher.py:33  N/A  run()  Default loaded configuration: {'display_config': <utility.config.DisplayConfig object at 0x10c6e4b80>, 'resource_config': <utility.config.ResourceConfig object at 0x10c6e4bb0>}
 2026-05-17 03:29:22,940   MainThread  󰿨 DEBUG  display.py:22  PyQuestEngine  __init__()  Initializing PyQuestEngine...
 2026-05-17 03:29:22,941   MainThread  󰿨 INFO   display.py:117  PyQuestEngine  run()  Running PyQuestEngine...
 2026-05-17 03:29:22,941   MainThread  󰿨 INFO   display.py:46  PyQuestEngine  initialize_display()  Initializing display...
 2026-05-17 03:29:23,634   MainThread  󰿨 DEBUG  display.py:54  PyQuestEngine  initialize_display()  Setting display mode to 800x600...
 2026-05-17 03:29:24,079   MainThread  󰿨 DEBUG  display.py:62  PyQuestEngine  initialize_display()  Setting window title to '"PyQuest"'...
 2026-05-17 03:29:24,081   MainThread  󰿨 DEBUG  display.py:36  PyQuestEngine  setup_states()  Setting up game states...
 2026-05-17 03:29:24,082   MainThread  󰿨 DEBUG  states.py:24  MenuState  __init__()  MenuState initialized
 2026-05-17 03:29:24,083   MainThread  󰿨 DEBUG  states.py:51  MenuState  load_menu_resources()  Loading game resources from resources/story.json...
 2026-05-17 03:29:24,085   MainThread  󰿨 DEBUG  states.py:61  MenuState  load_menu_resources()  Successfully loaded game resources from resources/story.json -> Data: {'menu': {'directory': 'menu', 'file': 'menu.txt', 'font': 'Arial', 'bg_music': 'menu_music.mp3'}, 'start': 'introduction', 'introduction': {'directory': 'intro', 'file': 'introduction.txt', 'font': 'Arial', 'bg_music': 'intro_music.mp3'}}
 2026-05-17 03:29:24,097   MainThread  󰿨 DEBUG  states.py:143  GameState  __init__()  GameState initialized
 2026-05-17 03:29:24,098   MainThread  󰿨 DEBUG  states.py:152  GameState  load_game_resources()  Loading game resources from resources/story.json...
 2026-05-17 03:29:24,098   MainThread  󰿨 DEBUG  states.py:162  GameState  load_game_resources()  Successfully loaded game resources from resources/story.json -> Data: {'menu': {'directory': 'menu', 'file': 'menu.txt', 'font': 'Arial', 'bg_music': 'menu_music.mp3'}, 'start': 'introduction', 'introduction': {'directory': 'intro', 'file': 'introduction.txt', 'font': 'Arial', 'bg_music': 'intro_music.mp3'}}
 2026-05-17 03:29:24,099   MainThread  󰿨 INFO   display.py:67  PyQuestEngine  load_resources()  Loading resources...
 2026-05-17 03:29:24,101   MainThread  󰿨 DEBUG  display.py:127  PyQuestEngine  run()  Entering main loop...
 2026-05-17 03:29:26,111   MainThread  󰿨 DEBUG  states.py:106  MenuState  handle_input()  Selected menu option: Start Game
 2026-05-17 03:29:26,112   MainThread  󰿨 DEBUG  states.py:111  MenuState  process_options()  Processing menu option: Start Game
 2026-05-17 03:29:26,113   MainThread  󰿨 DEBUG  states.py:113  MenuState  process_options()  Starting new game...
 2026-05-17 03:29:26,114   MainThread  󰿨 DEBUG  display.py:105  PyQuestEngine  update()  Changing state to game...
 2026-05-17 03:29:26,743   MainThread  󰿨 DEBUG  states.py:179  GameState  handle_input()  Escape key pressed. Transitioning back to menu...
 2026-05-17 03:29:26,746   MainThread  󰿨 DEBUG  display.py:105  PyQuestEngine  update()  Changing state to menu...
 2026-05-17 03:29:27,486   MainThread  󰿨 DEBUG  states.py:106  MenuState  handle_input()  Selected menu option: Exit
 2026-05-17 03:29:27,490   MainThread  󰿨 DEBUG  states.py:111  MenuState  process_options()  Processing menu option: Exit
 2026-05-17 03:29:27,492   MainThread  󰿨 WARNI  states.py:128  MenuState  process_options()  Exiting game...
```

## Problem

This looks good on the stream logging, but does not do justice to the file logging. Something needs to be done for that part...