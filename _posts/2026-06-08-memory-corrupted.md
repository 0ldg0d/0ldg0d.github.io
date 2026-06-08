---
title: Memory Corruption
description: Personal programming memory is getting corrupted.
date: 2026-06-08 12:16:35 +0530
categories: [Programming, General, Personal]
tags: [programming, personal]
---

## My own memory is getting corrupted.

Yes, you have read it correctly, my own memory is getting corrupted. At least that is what I think is happening.
Why do I say that? Simple, I am now taking more and more time in order to resolve Segfaults in C code. As a matter of
fact, I have kind of started to forget C and C++ programming altogether.

Yesterday, while working on a project of mine, I wrote the following:

```c
if (some_condition) {...}
else: ...
```

Are you able to see the mistake? No! Well, let me point it out - I have mixed Python like syntax with that of C. Take a deeper
look at the `else` portion, I added a `:` at the end of the keyword. Why? I have no idea.

The issue is, I was looking at this line of code and still took almost 40 seconds to figure out that I had made this mistake.
There were others as well. Since I have become accustomed to debugging using Log lines and all, I have become rusty with GDB.
This resulted in me taking a long time to resolve a segfault which was caused by my incorrect use of `strtok` output in my `strcat`
code. I should have seen/spotted this issue the moment I took a look at the code. However, that was not the case. I took a hard
look at the code, loaded the debuggable binary in GDB and then proceeded with adding breakpoints and then finally running the code.

## Sense of shame and guilt.

I am engulfed with an immense sense of shame and guilt. This is primarily because C and C++ are two of the most loved languages of
mine, despite the fact that people crib about security and stabilty. I have written over 12 fully features programs which I used
for myself in my day to day programming workflow. I am always inspired by the sheer amount of software which has been written with
C and C++ and they are powering, working without any flaws.


## What does this mean for me?

This means that I need to work on writing more and more C code, along with C++. The practice should not go away, despite the recent
crunch of time that I am going through. I need to be at the top of my game in order to be fluent in C and C++. Hopefully that happens
soon. This memory corruption needs to go away and I need to fight internally without anyone knowing that I am facing this issue.

It is so shameful that I am forgetting things, especially things that I am fond of. I should be more careful with what information I
need to retain in my brain and what should be kicked out or not at all accepted as such. This needs to be the top priority for me.

## Silver Lining.

There is a silver lining though, the moment I started working with C and C++, it slowly started to open the gates for me and I was able
to, as of the last attempt, able to resolve one segfault issue with the help of GDB itself. I was able to recognise the issue pretty
early, even before loading and running the program using GDB. However, I still needed GDB in order to confirm my suspicion.

I am banking on this aspect and hoping that I will be able to bounce back with both C and C++ as well as abide by my plan for learning
golang and Lua. Keeping the fingers crossed and hoping for the best. For now, this is it.
