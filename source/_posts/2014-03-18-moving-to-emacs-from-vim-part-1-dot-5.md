---
layout: post
title: "Moving to Emacs from Vim Part 1.5"
date: 2014-03-18 15:10:15 +0900
comments: true
categories:
---


So in the [last post][], we got Emacs working nicely with Vim keybindings using evil. But there is one keybinding that immediately started bugging me:

Emacs binds `<Ctrl>-h` to the help command. For example, you press `<Ctrl>-h k` and then press a key to get information on functions bound to that key; you press `<Ctrl>-h f` and enter a function name to get help on that function, etc.

Now I don't know about you, but having basically lived in the terminal for years now, I am used to `<Ctrl>-h` sending a backspace character, which works in the shell and in Vim. And while help is undeniably useful (Vim's help is really great, by the way), I'm not sure it should be bound to a key in the **home row**. So let's fix that:

``` cl
(define-key key-translation-map [?\C-h] [?\C-?])
(global-set-key (kbd "M-h") 'help-command)
```

Add these lines anywhere in `~/.emacs.d/init.el`. I put them just above `(tool-bar-mode -1)`.

And that's it! You can happily use `<Ctrl>-h` to backspace to your heart's content. If you do want to access Emac's help, you can press `<Alt>-h` instead. By default, `<Alt>-h` is bound to `mark-paragraph`, which we don't need because evil allows us to select a paragraph by pressing `vip`, just like in Vim.

You can read more at the [Emacs Wiki][ewbackspace], which is where I found the above lines.


[last post]: http://cordarei.github.io/blog/2014/03/17/moving-to-emacs-from-vim/
[ewbackspace]: http://www.emacswiki.org/emacs-en/BackspaceKey
