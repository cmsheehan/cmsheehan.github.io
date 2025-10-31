---
author: ["cmsheehan"]
title: "Managing Dotfiles with GNU Stow"
date: "2025-10-29"
description: ""
tags: ["stow", "dotfiles", "how-to"]
ShowToc: true
TocOpen: false
---

## The Problem

You've spent months perfecting your dev environment. Then you get a new machine and have to do it all over again.

## The Solution

Store your config files in a Git repo. Use GNU Stow to symlink them where your system expects them. Clone and restore anywhere in seconds.

## What Are Dotfiles?

Configuration files that start with a period: `.bashrc`, `.zshrc`, `.gitconfig`, `.vimrc`. They control your shell, editor, Git settings, and more.

## Setup

### 1. Install Stow

```bash
# Arch
sudo pacman -S stow

# Debian/Ubuntu
sudo apt install stow

# macOS
brew install stow
```

### 2. Create a Private Repo

```bash
mkdir ~/.dotfiles
cd ~/.dotfiles
git init
```

**Important:** Keep your dotfiles repo **private**. Config files often contain paths, usernames, and other details that could leak secrets or make you a target.

### 3. Configure Stow to Ignore Git Files

Create a `.stow-local-ignore` file to prevent Stow from symlinking your `.git` directory:

```bash
echo ".git" > .stow-local-ignore
```

Without this, Stow would try to symlink your entire `.git` folder to your home directory, which would break Git operations in both locations.

### 4. Move Your Configs

```bash
mv ~/.bashrc ~/.dotfiles/
mv ~/.zshrc ~/.dotfiles/
mv ~/.gitconfig ~/.dotfiles/
```

### 5. Symlink with Stow

```bash
stow .
```

Stow creates symlinks from `~` back to `~/.dotfiles`. Your system finds the files where it expects them, but the real files live in your repo.

### 6. Commit and Push

```bash
git add .
git commit -m "Initial dotfiles"
git remote add origin git@github.com:yourusername/dotfiles.git
git push -u origin main
```

## Restore on New Machine

```bash
cd ~
git clone git@github.com:yourusername/dotfiles.git ~/.dotfiles
cd ~/.dotfiles
stow .
```

Done.

## Bonus: Track Installed Software

On macOS with Homebrew:

```bash
brew bundle dump --describe
```

Commit the `Brewfile` to your repo. Restore with `brew bundle install`.

## Tips

- Use GitHub's no-reply email in `.gitconfig`
- Don't track files that change constantly (`.zsh_history`)
- Build an install script as your setup grows
- Document your process in the README

## Resources

- [Dotfiles Community](https://dotfiles.github.io/)
- [Atlassian Dotfiles Tutorial](https://www.atlassian.com/git/tutorials/dotfiles)
- [Managing with GNU Stow and Git](https://dev.to/luxcih/dotfiles-managing-with-gnu-stow-and-git-5100)
- [Fireship Video](https://www.youtube.com/watch?v=r_MpUP6aKiQ&t=294s)

Configure once. Deploy everywhere.