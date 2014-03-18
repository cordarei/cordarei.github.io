---
layout: post
title: "Moving to Emacs from Vim"
date: 2014-03-17 12:47:22 +0900
comments: true
categories:
---


So you're a dyed-in-the-wool vimmer, but now you want to use Emacs? (Sorry, I meant *I*—*I* am a dyed-in-the-wool vimmer and *I* want to use Emacs.) Let us go on a journey together…

## Reasons

Here are some reasons I can think of to use Emacs:

- to get used to Emacs, for instance to be able to work on systems that don't have Vim installed (shudder)
- to make use of some Emacs plugin that doesn't have a good substitute in Vim (e.g. org-mode)
- because Emacs GUI works better in Windows than GVim [^1]
- because Emacs Lisp is nicer than Vimscript [^2]

A lot of my motivation has to do with my workflow, which is a mix of tmux and vim in the terminal. It works pretty well, especially with Tim Pope’s [vim-tbone][tbone] plugin, which gives you `Tyank` and `Tput` commands to communicate with the tmux copy buffer. However, I got curious what it would be like for the editor and shell to be more equal, which Emacs seems to offer.

> “Why use Emacs in a terminal when you can use a terminal in Emacs?”

> — Me

## Preparation

Since you are like me, I’m assuming you don’t have any Emacs configuration files, no `.emacs` or `.emacs.d/`. I recommend creating a directory `emacs.d/` (no leading ‘.’) in a git repository somewhere.[^3] You can then check out the repository on any system and symlink the `emacs.d/` directory to `~/.emacs.d/` (with leading dot). You can look at my [dotfiles][] repo for reference if you like.

Anyway, now inside the `emacs.d/` directory create a file named `init.el`. Emacs will read the file at `~/.emacs.d/init.el` during startup; we will be putting all of our customizations inside this file for now. Before we put anything into `init.el`, you can try starting up Emacs to see what it’s like. To move around, `<Ctrl>-p` and `<Ctrl>-n` move up and down, and `<Ctrl>-f` and `<Ctrl>-b` move forward and backward. To quit press `<Ctrl>-x <Ctrl>-c` (that is, hold down `<Ctrl>` and press `x` and then `c`).

Since Emacs is modeless, `hjkl` and other printable characters always insert themselves, and thus to execute commands you have to use modifier keys. `<Ctrl>` is okay, since [you should have CAPS LOCK mapped to Control][swapcaps] [^4]  (or [for Windows][windowsswap]), but Emacs makes you press the `Alt` key, which I am not going to put up with, so we’ll be using an Emacs plugin called ‘evil’ to make Emacs more like Vim.


## Onward!

Don’t worry, I won’t make you edit your `init.el` from inside Emacs (you can try it if you want to, but you’ll need to quit and restart Emacs every time you change it), so let’s just open it inside Vim:

``` bash
$ vim ~/.emacs.d/init.el
```

and add the following lines:

``` cl
(require 'cl)
(require 'package)

(add-to-list 'package-archives
             '("melpa" . "http://melpa.milkbox.net/packages/") t)
```

The default Emacs package repository at [http://elpa.gnu.org/](http://elpa.gnu.org/) contains a limited set of packages, and most importantly for our purposes does not contain [evil][evil-ewiki], so we are adding the [MELPA][] repository, which does have evil, along with a lot of other packages. The first two lines import the `cl` package and the `package` package. The `package` package imports things related to packages (duh), like the `package-archives` variable or the `package-install` function we will use shortly. The `cl` package is the Common Lisp compatibility package; I’m not actually sure of everything it does, but some things seem to need it and it’s probably just easiest to add it in at the very beginning.

At this point, you can start up Emacs, and from the ‘Options’ menu choose ‘Manage Emacs Packages’. You should see a list of packages, and if you scroll down a bit, you should find the ‘evil’ package listed, along with some evil plugin packages like ‘evil-leader’. Try clicking on the blue ‘evil’ link. A window split should open up with the bottom half showing details for the ‘evil’ package. There is an `Install` button that you can press to install the package; alternatively, you can (in the top window) press `i` on the ‘evil’ package line to mark it for install and then press `x` to install it. Either way, some messages will flash by in the bottom window about compiling whatever, possibly some warnings which you can ignore (I do), and then evil will be installed! Great!

Of course, nothing changes because it isn’t activated automatically. First, click in the top window (with the package list), type `<Ctrl>-x 1` to close the bottom window (if you opened it), and type `q` to quit the package manager interface. You should return to the ‘GNU Emacs’ start screen. Open up the scratch buffer by going to the ‘Buffer’ menu and selecting ‘\*scratch\*’. Emacs automatically creates this \*scratch\* buffer every time you run it and the contents are not saved when Emacs quits. It’s useful for typing stuff, which is what we’re going to do. First try typing something and see that everything is still just like it was, typing letters inserts them and the `<Ctrl>-` keys move around.

Now, type `<Alt>-x` (the only time I’ll ask you to press the Alt key, I swear!) and at the prompt (in the minibuffer at the bottom of the screen) type `evil-mode` and hit Enter. What changed? Try typing `k`.

!

The cursor should have moved up a line! If you look at the mode line (the status line at the bottom of the window) you should see `<N>`. That indicates that you’re in the evil normal *state*. In evil, *states* are the equivalent of Vim’s modes, because Emacs already uses the word ‘mode’ for something more like Vim’s ‘filetype’. If you type `i` or `a` you will enter *insert* state (Vim’s insert mode). At this point you can just start doing whatever you would do in Vim. Some things won’t work, a few things will be slightly different, but mostly things will work in evil just as they do in Vim—including things that don’t work in `vi`, because evil is a *Vim* mode for Emacs, not a *vi* mode.

Ex (colon) commands work as well, which I understand is a fairly recent change. You can type `:ls<Enter>` and the Emacs ‘Buffer List’ buffer will come up. You can hit `q` here to close the buffer list and return to the scratch buffer, because evil leaves some keybindings alone in some Emacs modes. Unfortunately, you **can’t** hit `Enter` to open up a buffer[^5]—it just moves down a line. This is a good place to use another evil feature: by default, the `\` key escapes one key press to execute the original Emacs command bound to that key; if you hit `\` and then `Enter`, it should open the buffer you selected.


## Leveling Up

This is all well and good, but currently every time you start Emacs you will have to type `<Alt>-x evil-mode <Enter>`—the horror! So let’s close Emacs and go back to our Vim where we have `init.el` open. Add these two lines to the end:

``` cl
(package-initialize)

(evil-mode t)
```

Emacs actually calls `package-initialize` automatically, but only after it loads `init.el`, and we need it to use things in packages, so we call it ourselves. The last line activates evil, so that every time we start Emacs, it will start in evil mode. Try it: (re-)start Emacs, see that the mode line shows `<N>` and you can move around with `hjkl`, go to the \*scratch\* buffer (try `:b<Space>scr<Tab>` and hit Enter), type– whatever, you know what you’re doing by now.


## Going Further

So this is great, again, but what happens when you move to a different system, or reinstall your OS? You’d have to install evil manually again. Wait—I know what you’re going to say, and I have the answer:


``` cl
(defun ensure-package (package)
    (unless (package-installed-p package)
      (package-refresh-contents)
      (package-install package)))

(ensure-package 'evil)
```

Put these lines after `(package-initialize)` and before `(evil-mode t)`, then to test run `rm -rf ~/.emacs.d/elpa` in your shell (`.emacs.d/elpa/` is the directory where Emacs keeps the packages it installs) and restart Emacs. You should see Emacs downloading and installing evil in a split window. Unfortunately it leaves the compilation log buffer open in the bottom window, which you can close by typing `<Ctrl>-w o` in the top window. (I don’t have a solution for this yet, sorry).


## Extra Credit

### Lose the Toolbar and Menu

Do you like that toolbar that takes up like 5% of the screen? Do you want to be constantly reaching for the mouse to dig through a bunch of menus? (A: No!)

That’s what I thought. Put these lines at the end of your `init.el`:

``` cl
(tool-bar-mode -1)
(menu-bar-mode -1)
```

Restart Emacs, and— ahh! Much better! You can never have too much screen real-estate. If you do want to show the menu temporarily, just type `:menu-bar-mode` to toggle the menu on and off.


### Colors!

Okay, it has colors, but do we really think the default color scheme is the best we can do? Personally, I suggest the [monokai theme][], for which we can install a package from MELPA. Now, we *could* just add `(ensure-package 'monokai)` to the `init.el` file, but I think we can do better. Delete the `(ensure-package 'evil)` line and add the following:

``` cl
(setq packages '(
                 evil
                 monokai-theme
                 ))

(mapcar 'ensure-package packages)
```

If you’re not familiar with Lisp, `(setq x '(…))` sets the value of variable `x` to whatever the other argument is, in this case a quoted list. `(mapcar 'f x)` applies the function named `f` to each member of list `x`. In other words, we call `ensure-package` on every package named in `packages`. For just two packages, this is more work than necessary, but as you want to add more packages, all you need to do is add the package name to the list, and it will be installed next time you start Emacs!

Of course, just installing the package doesn’t cut it, so add `(load-theme 'monokai t)` to the end of your `init.el` (you can put it anywhere, actually, I just think it feels right as the last thing in the file).

If you don’t like Monokai, you can try [Solarized for Emacs](https://github.com/sellout/emacs-color-theme-solarized) (add `color-theme-solarized` to `packages` and `(load-theme 'solarized-[light|dark] t)` instead of `(load-theme 'monokai t)`).


## tl;dr

Download the file at https://gist.github.com/cordarei/9600170 to `~/.emacs.d/init.el`, start Emacs, and enjoy Vim keybindings in Emacs!



[^1]: This has been my experience at least, for two reasons: editing Japanese in GVim on windows has sucked for me; and the package manager in Emacs 24 beats fapping around with git/pathogen/Vundle in windows.

[^2]: Vimscript is really not that bad, it's just that Lisp is better, plus Emacs supports things like inferior processes.

[^3]: GitHub is nice.

[^4]: I found someone saying <a href="http://ergoemacs.org/emacs/swap_CapsLock_Ctrl.html">you should NOT swap Caps Lock with Control</a> for the first time when writing this post; YMMV but I prefer Control in the Caps Lock position.

[^5]: I thought this would work before I tried it just now, but it didn’t :(


[tbone]: https://github.com/tpope/vim-tbone
[dotfiles]: https://github.com/cordarei/dotfiles

[evil-ewiki]: http://www.emacswiki.org/emacs/Evil
[MELPA]: http://melpa.milkbox.net/
[monokai theme]: https://github.com/lvillani/el-monokai-theme

[my-current-init-el]: https://github.com/cordarei/dotfiles/blob/395b49270ada434892c0fe36e14f7c95ca41df6e/emacs.d/init.el

[swapcaps]: http://www.emacswiki.org/emacs/MovingTheCtrlKey
[windowsswap]: http://stackoverflow.com/a/840716/32683
[noswap]: http://ergoemacs.org/emacs/swap_CapsLock_Ctrl.html
