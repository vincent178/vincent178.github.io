---
layout: post
title: "Setup Arch Linux on Surface Go3"
date: 2022-03-16T18:18:14+08:00
draft: false
showFullContent: true
tags: ["Linux", "Surface"]
images: ['/linux/arch-on-surface.png']
---

<!--more-->

I'm currently using EndeavourOS, comparing to Manjaro, this is more backbone Arch. I use bspwm version, which is community supported by default in EOS. There's very minimal default application builtin which I all appreciate, like nice wifi manager and file exploer.

![arch on surface](/linux/arch-on-surface.png)

## Install Packages

```bash
$ yay -S neovim alacritty ranger fzf neofetch blueberry
```

## Basic Environment

First setup zsh shell:

```bash
$ sh -c "$(curl -fsSL https://raw.github.com/ohmyzsh/ohmyzsh/master/tools/install.sh)" # install oh-my-zsh
$ git clone https://github.com/agkozak/zsh-z $ZSH_CUSTOM/plugins/zsh-z # install z command
$ git clone https://github.com/zsh-users/zsh-autosuggestions ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-autosuggestions # install fish-like auto suggestion plugin for zsh
```

and update .zshrc to include both plugins

```bash
plugins=( 
    # other plugins...
    zsh-z
    zsh-autosuggestions
)
```

Setup dotfiles

```bash
$ git clone git@github.com:vincent178/dotfiles.git
$ cd dotfiles 
$ mv ~/.config ~/.config.bak
$ ln -fs $PWD/.config ~/.config
```

Setup neovim, when start neovim first time, it will download plugins automatically
```bash
$ cd $HOME/.vim/autoload/plugged/telescope-fzf-native.nvim
$ make
```

## Install Nerd Font

```bash
$ git clone --depth 1 git@github.com:ryanoasis/nerd-fonts.git
$ cd nerd-fonts
$ ./install.sh SourceCodePro # This is the font I used for alacritty
```

## Setup Docker Environment
```bash
$ yay -S docker
$ usermod -aG docker $USER
```

and logout

