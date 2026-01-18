---
title: Credentials Manager
description: A project for managing the credentials for various web applications.
date: 2026-01-19 01:10:13 +0530
categories: [Programming, Project, Credential_Manager]
tags: [programming]
---

## Project Idea

I have always been keeping a track of all my accesses(credentials, files for authentication) using a standard text file which is uploaded
into Google Drive. Over time, this file has grown and I have now started to fear that one day I might lose this file in case something breaks
on the internet. Given the recent outages of Cloudflare and AWS, I thought of creating a Credentials manager project which needs to allow me
to perform certain operations.

### Credentials Manager

The aim of this credentials manager is to store all the credentials and files in a secure file/database and when started, should read the data
from the source and then show the details in a proper way such that I can copy the credential/check the content of the data from the file to be
used for authentication.

Given the requirement is that of a Desktop application and preferably with an UI, I need to decide on a GUI library. WxPython is a decent enough
library for me, having worked with the same earlier. I need to check if I could place some content on the clipboard of the machine. This feature
is non-negotiable for me.

`Pyperclip` is a module about which I had heard about earlier, the details of the package could be found
[here](https://pyperclip.readthedocs.io/en/latest/). I think on the `pyperclip.copy("...")` function should be enough for setting up the clipboard
with the right information.

#### Project Requirements

- Should be able to allow the user to copy the credentails to the clipboard.
- Should be able to allow the user to add new credentails.
- Should be able to allow the user to generate new passwords based on constraints.

#### Tools of choice

- Vim/NeoVim
- Python 3.13.9 (Using Anaconda for now, the Mac has an older Python version and I am not keen on using `pyenv`)
- WxPython for the GUI.
