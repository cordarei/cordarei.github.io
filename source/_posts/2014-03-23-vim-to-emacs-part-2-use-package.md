---
layout: post
title: "Vim to Emacs Part 2: use-package"
date: 2014-03-23 14:54:34 +0900
comments: true
categories: 
---

As I start to add more packages and customizations to my emacs configuration, it would be a good idea to have a plan for organizing things to keep it manageable. Funnily enough, there is a package called [use-package][] that seems like it will be useful in this endeavor, so let's use it.

First, you can read more about `use-package` at its [Github repository][use-package], [this reddit thread][reddit], or [this blog post][blog]. The main advantages of `use-package` for my purposes are:

- the `:ensure` keyword automatically installs packages (replaces my `ensure-package` function)
- it helps centralize configuration related to each package
- it can defer executing code for packages which are only needed in certain modes

The last two are not such a concern yet, but will be useful in the future.

I have refactored the `init.el` file from before. You can see the new contents below (with comments), or just [download the raw file][raw].

``` cl
;; setup for requiring and installing packages

(require 'cl)
(require 'package)

;; add extra ELPA archives
(add-to-list 'package-archives
             '("melpa" . "http://melpa.milkbox.net/packages/") t)

;; initialize package system
(package-initialize)

;; get list of packages if not present
(unless package-archive-contents
  (package-refresh-contents))

;; make sure use-package is installed (we'll use it to install other packages)
(unless (package-installed-p 'use-package)
  (package-install 'use-package))

;; make use-package available
(require 'use-package)


;; set up individual packages

;; evil-mode
(use-package evil
  :ensure evil
  :config
  (progn (evil-mode 1)))

;; monokai theme
(use-package monokai-theme
  :ensure monokai-theme
  :config
  (progn (load-theme 'monokai t)))


;; set up interface customizations

;; keybindings:

(define-key key-translation-map [?\C-h] [?\C-?])
(global-set-key (kbd "M-h") 'help-command)

;; GUI settings:

(tool-bar-mode -1)
(menu-bar-mode -1)
```

I got rid of `ensure-package` and made some changes based on the Stack Overflow question [here](http://stackoverflow.com/questions/14836958/updating-packages-in-emacs). It makes sense to me for updating packages to be manually invoked, so we can think of our process as: for a new system with no packages (and no package-archive-contents), we first fetch the list of packages, install `use-package`, and use `use-package` to install and configure our other packages; in normal use, our packages are already installed and `use-package` configures them. When we want to add a package, just add `(use-package <package> ...)` to our config file and let `use-package` install it for us.

For upgrading packages, it may make sense to have a function to do everything necessary (I haven't looking into what that would be) that I can invoke in one step. Alternatively, I could just nuke `~/.emacs.d/elpa/` -- (not-so-)instant upgrade!


[use-package]: https://github.com/jwiegley/use-package
[reddit]: http://www.reddit.com/r/emacs/comments/1r4qvs/john_wiegleys_usepackage_and_bindkey/
[blog]: http://ericjmritz.name/2013/11/25/simplify-emacs-configuration-with-use-package/
[raw]: https://gist.githubusercontent.com/cordarei/9719297/raw/1ede54fd18d59c31fbbb6de03a03cdcad6c4b1bb/init.el
