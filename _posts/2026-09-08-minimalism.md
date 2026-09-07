---
title: Minimalism
description: Minimalism in design and approach, a dive into the world of simplicity.
date: 2026-09-08 03:58:42 +0530
categories: [Programming, General]
tags: [programming, general]
---

## Minimalism

Of late, I have become a fan of minimalism. "As less code changes as possible, as simple as possible, as minimal as possible" etc. are some of the things that have occupied
my mindset of late. I think this also stems from the fact that, the more additional functionalities are added, the more the user is distracted.
Unix had gotten this right, _"Do one thing and do it very well"_ and I feel that this approach when applied to anything in life really brings out the best possible
outcome. Whether it is programming, writing novels, anything that is used for creative purposes right down to the level of the pen itself.
The only exceptions in this case, in my opinion, are the Japanese pencils(Kurutoga and the like) which does indeed solve a problem with mechanical pencils which none of the
other mechanical pencil manufacturers have addressed.

However, we should not digress much on this but rather stay to the point. Minimalism sticks and the main _mantra_ for sticking to minimalism is to break down any solution
being offered in smaller chunks. Each chunk being designed and created using the idea in mind - _"Do one thing and do it very well"_.

Of late, I was feeling that my editor of choice(currently neovim) has become a lot bulky and is getting in the way of my editing code. Mind you, it is fast, but with all the
bells and whistles that I had added, somewhere in the back of my mind I was realizing that it was slow. Painfully slow when working over an SSH connection. If the network
connection is stable, the performance is superb. In case there are fluctuations, it felt laggy. Especially the part where the cursor movement sometimes stalled the entire
editing flow.

This started to cause an itch and in order to relieve myself of this itch, I went down the rabbit hole.

## Current configuration

My current configuration uses LazyVim along with some plugins like Goto Preview, AI Chat clients, smooth cursor movement etc. Once I started seeing the performance degradation,
for example, the cursor animated and moved slowly over a period of time, I started thinking about the configuration I really want.

My current configuration consists of the following:
1. Show list characters and allow for toggling
2. Integrate Lazy Git
3. Telescope for finding files, switching buffers, diagnostics or grep-ping
4. Smooth cursor movement
5. GitHub Copilot
6. Fancy status bar(though I was using default ones that are provided with LazyVim)
7. Symbols outline
8. Undo history
9. Floating terminal
10. Catppuccin colorscheme - I am not sure why I wanted it at the moment I added it
11. LSP (for various languages I usually work with, primarily Python, C and C++)
12. Easy commenting and un-commenting of code
13. Some specific auto-groups having custom changes to `tabstop`, `shiftwidth` etc.
14. Search for unicode symbols(along with support for Nerdfont)
15. Peek into code

As one can see above, the list is pretty long and there are some plugins which are provided by LazyVim itself, which might not be required for me. The overall process of
setting up the editor with the right keymaps was also not straight forward. It was a shameful fact that when some of my colleagues asked me share with them the details of the
steps for setting up on their own, I was not able to give them a straight instruction.

With minimalism in mind, I started going through my requirements.

## Requirements

I happened to look at a coding livestream session of Rene Rebe and I was amazed at what he could do with almost vanilla Vim. I thought I will at least use the same type of
colorscheme that he was using. My minimalism will start with the most basic thing - the colorscheme needs to be contrasting without having too many colors. Upon searching for a
simple colorscheme which would highlight the code on shades of grey, white with dark background, I settled on `Coal.nvim`.

Then I started listing out the next set of things that I absolutely require in order to write code and not let the enditor get in the way or distract me with any of the plugins:

1. Minimal colorscheme - Coal
2. Telescope for finding files, switching buffers, diagnostics or grep-ping
3. Symbols outline
4. LSP (for various languages I usually work with, primarily Python, C and C++)
5. Easy commenting and un-commenting of code
6. Some specific auto-groups having custom changes to `tabstop`, `shiftwidth` etc.
7. Search for unicode symbols(along with support for Nerdfont)
8. Peek into code, if required open the file

I could see that I could reduce almost half of the requirements that I already had. The one that I used for official purposes, can remain the same. However, this one that I was now
thinking of, should be as minimal as possible. The other aim was to make sure that if I wish to set up the editor in a new system or elsewhere, it should be as simple as cloning the
configurations in `~/.config/nvim` directory and then issuing the `nvim` command.

## Neovim 0.12.x

Neovim 0.12.x (I am currently using Neovim 0.12.5) comes with a out-of-the box approach and has a packer included. With Vim pack, I rewrote the entire plugins loading, the entire set
from scratch in Lua where the user should be able to just clone the repository and be done with the setup process.

## Aftermath

While I am writing this post, I can assure you, now I am actually enjoying editing files(code or otherwise) using my editor of choice. It is no more getting in the way of editing.
On the contrary, now it feels more inline with my workflow and that it is just waiting for my command to perform the action. I did a check for performance over SSH in a laggy
connection and I was still able to edit the files without any lags or slowness. The editor responded as I wanted it to respond.

All in all, I am pretty happy with the way my current configuration has turned out.
