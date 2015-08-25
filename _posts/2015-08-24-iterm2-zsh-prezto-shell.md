---
layout: post
title: Prettier OSX terminal with iTerm2 and Prezto
tags: tools, osx
---

I wanted to describe my current Mac OS X shell setup. I've switched from the default Terminal.app to [iTerm2][iterm]. This includes the following:

* switched from bash to [zsh][zsh]
* use [prezto][prezto] framework for a prettier colorful prompt
* set up a few iTerm2 triggers e.g. to capture and highlight or show Android gradle build errors etc.

I'll list every step I took to install all the various pieces:

**1. preparation**

First install [Homebrew][brew] if you don't have it yet:

    ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"

And we also need [caskroom][cask] to install apps with binary installers:

	$ brew install askroom/cask/brew-cask

**2. install zsh**

	$ brew cask install zsh

**3. install iTerm2**

    $ brew cask install iterm2

**4. configure zsh as the new default shell**

Just for interest's sake, this is to show that originally I was on bash:

	$ echo $SHELL
	/bin/bash
	$ echo $BASH_VERSION
	3.2.57(1)-release

Now change my default shell to zsh:

	$ chsh -s /bin/zsh

Re-open iterm2 and check the shell version again to confirm zsh is running:

	% echo $SHELL
	/bin/zsh
	% echo $ZSH_VERSION
	5.0.5

Specify custom location for my dot-files so I can save them in git:

	% mkdir ~/dotfiles
	% cat <<EOT > ~/dotfiles/.zshenv
	#!/bin/zsh
	export ZDOTDIR=~/dotfiles
	source "\${ZDOTDIR}/.zprofile"
	EOT
	% touch $ZDOTDIR/.zshrc # will put my fav aliases and functions here
	% ln -s ~/dotfiles/.zshenv ~/.zshenv

Quick example of some [handy aliases][profile] etc in `.zshrc`:

	% export ZDOTDIR=~/dotfiles
	% cat <<EOT >> ~/dotfiles/.zshrc
	source "\${ZDOTDIR:-\$HOME}/.zprezto/init.zsh"
	export JAVA_HOME="`/usr/libexec/java_home -v '1.7*'`"
	alias ll='ls -FGlAhp'
	alias gca='git commit -a --amend'
	alias gr='./gradlew'
	# etc...
	EOT

**5. get prezto**

	% git clone --recursive https://github.com/sorin-ionescu/prezto.git "${ZDOTDIR:-$HOME}/.zprezto"

Symlink some default prezto config files:

	% setopt EXTENDED_GLOB
	for rcfile in "${ZDOTDIR:-$HOME}"/.zprezto/runcoms/^README.md(.N); do
	  ln -s "$rcfile" "${ZDOTDIR:-$HOME}/.${rcfile:t}"
	done

Re-open the iterm2 window to see if all the dotfiles are set up correctly (i.e. zprezto modules are loading without errors).

Set the theme (I use `steeef` built-in theme), modules (like git) etc in `.zpreztorc`:

	% subl $ZDOTDIR/.zpreztorc 

After setting up vi key bindings, `^R` for history substring search stopped working. View key bindings - this confirms `^R` is now bound to "redisplay":

	% bindkey -L | ag '\^R'
	bindkey "^R" redisplay

I added this line to my `.zshrc` file to enable `^R` history search again:

	bindkey "^R" history-incremental-search-backward

This setup was inspired by this [post](http://mikebuss.com/2014/02/02/a-beautiful-productive-terminal-experience/) by [Mike Buss](https://twitter.com/michaeltbuss).

[brew]: http://brew.sh
[cask]: http://caskroom.io
[iterm]: https://github.com/gnachman/iTerm2/
[prezto]: https://github.com/sorin-ionescu/prezto
[profile]: https://gist.github.com/natelandau/10654137
[zsh]: http://www.zsh.org