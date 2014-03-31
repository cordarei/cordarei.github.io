---
layout: post
title: "Vim to Emacs Part 3: Startup"
date: 2014-03-31 18:06:27 +0900
comments: true
categories: 
---

Here are some recent improvements I made to my Emacs startup. The full file with these changes is [on GitHub](https://github.com/cordarei/dotfiles/blob/728d8ff01ef70e241daaa4e9e4fb82e5437094fc/emacs.d/init.el).


## Disable toolbar and menu at the beginning

Since there can be a noticeable delay while Emacs processes the init file, especially if it is downloading packages, it's better to customize the GUI as early as possible to avoid the window jumping around. I also made `monokai-theme` the first package I `require` after `use-package`, although I think this has limited utility.

Add these lines to the beginning of `~/.emacs.d/init.el`:

``` cl
;; remove GUI stuff
(when (fboundp 'menu-bar-mode) (menu-bar-mode -1))
(when (fboundp 'tool-bar-mode) (tool-bar-mode -1))
(when (fboundp 'scroll-bar-mode) (scroll-bar-mode -1))
```

## Don't show GNU start screen

Get rid of the GNU splash/startup screen, and also remove the unnecessary message in the `*scratch*` buffer:

``` cl
;; show blank *scratch* buffer at start
(setq inhibit-startup-screen t)
(setq initial-scratch-message nil)
```

## Disable bell

I can't really express just how much I hate the bell. I disable it in Vim, I once even went so far as to unhook the internal PC speaker from the motherboard of one machine. It's often suggested to `(setq visual-bell t)`, but that makes Emacs flash the window which is almost as annoying. Here's how to disable it entirely (from [this StackOverflow answer][so]):

``` cl
;; disable bell
(setq ring-bell-function 'ignore)
```

[milky]: http://milkbox.net/note/single-file-master-emacs-configuration/
[so]: http://stackoverflow.com/a/11679711
