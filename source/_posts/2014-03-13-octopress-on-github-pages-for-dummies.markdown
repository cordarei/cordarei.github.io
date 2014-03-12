---
layout: post
title: "Octopress on GitHub Pages for Dummies (Me)"
date: 2014-03-13 02:42:08 +0900
comments: true
categories: 
---



Follow these steps to create a blog using Octopress on GitHub. I'm assuming you have a system ruby installed; preferably you're using Arch Linux, because that's what I used.

### 1. First -- don't read the documentation on github.com

Don't bother reading the help documents about GitHub Pages and Jekyll at https://help.github.com/articles/using-jekyll-with-pages ; in Octopress everything is generated outside of Github.

### 2. Don't run `git clone https://github.com/imathis/octopress` yet

First, if you have a user pages `<user>.github.io` repository on GitHub -- delete it! Octopress works by forking the Octopress repository and adding your content on top of it. I'm not even sure if it's possible to merge the history from two different repositories in git, but I bet it's not worth the trouble. Just delete it (back it up first if it has anything in it -- or you could just rename it).

Now create a new `<user>.github.io` repository, and UNCHECK all the boxes: no `README`, no license, no `.gitignore` -- we want a blank repository with no commits.

### 3. Okay, **now** `git clone https://github.com/imathis/octopress`

### 4. Run `gem install bundler`

You need bundler to do everything else, and on Arch Linux it should install under $HOME/.gem/

### 5. **DON'T** run `bundle install`! (If you're using Arch Linux)

This is important: bundle will ignore that gem is set to install locally (`--user-install`) by default on Arch Linux, and install everything in `/usr`. (Okay, you can cancel when it asks for your sudo password, but still). Plus it will install the dependencies for Octopress/Jekyll mixed with anything else you have installed, which is annoying, at least.

You will need to add `export GEM_HOME=$(ruby -e 'puts Gem.user_dir')` to your shell (~/.zshrc or ~/.bashrc) to get bundler to install gems under your home directory (see https://wiki.archlinux.org/index.php/ruby#Bundler ). However, this is negated by our next step:

Run `bundler install --path .bundle`. This will make bundler install the dependencies listed in the `Gemfile` into `.bundle/`, which is much easier to clean up [^1]

### 6. Don't run `rake setup_github_pages`

Since we made bundler install the gems into `.bundle/`, they aren't on the PATH. To run the rake installed by bundler, we would need to type `bundle exec rake <task>`. I for one refuse to type that more than once, so do `alias rake='bundle exec rake'` to make things easier temporarily (I don't have a permanent solution for this yet).

### 7. Whew! Now run `rake setup_github_pages`

### 8. Don't run `rake generate` yet

First edit `_config.yml` to customize your site: change some things like `title:`, `subtitle:`, `author:` and maybe add your username to `github_user:` if you want.

### 9. Finally! Run `rake generate` and then `rake deploy`

  - you should see a bunch of output, some of which may be `git` telling you about missing tracking information for the current branch (don't worry about this yet)

  - you should have `source`, `sass`, `_deploy`, and `public` directories now

  - if you do `git status`, you should see changes in `Rakefile` and `_config.yml`, and untracked directories `sass` and `source`

  - you should be able to see your new site at `http://<user>.github.io/` now (GitHub documentation says it may take a few minutes; I personally haven't seen a delay in updating yet).

### 10. Now do `git add .` and `git commit`, write a commit message and then `git push origin source`

With Octopress, the source for generating the site is kept on the 'source' branch, and the generated files are committed and pushed to the 'master' branch automatically by `rake deploy`

---

Now, you could continue to develop your site, add some posts and whatnot -- but I recommend you do two things first:

  - `cd ..; rm -rf octopress`
  - `git clone -b source git@github.com:<username>/<username>.github.io`
  - `cd <username>.github.io`
  - `git clone -b master git@github.com:<username>/<username>.github.io _deploy`[^2]
    
This will help `git` get straightened out with all of the remote tracking branches etc. The only problem is you have to run `bundle install --path .bundle` again -- oops. Of course I thought of that before deleting the original directory...


[^1]: technically, it is *possible* to uninstall gems (see http://stackoverflow.com/a/21385516/32683 or http://stackoverflow.com/a/8095234/32683 -- where would the world be without StackOverflow?)

[^2]: see, e.g., http://robstrube.com/blog/2014/02/21/setting-up-an-existing-octopress-site-on-a-new-system/ or http://weishi.github.io/blog/2013/07/24/setup-an-existing-octopress-repository-after-git-clone/ -- seriously, do the Octopress people really expect no one to ever move to a different computer?
