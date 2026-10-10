---
author: ["cmsheehan"]
title: "Managing Dotfiles with GNU Stow"
date: "2025-10-29"
description: ""
tags: ["stow", "dotfiles", "how-to"]
ShowToc: false
TocOpen: false
cover:
  image: "images/dotfiles.png"
---

Setting up a dev environment the way you like it takes a long time, and on a new machine you have to do it all again. Keeping your config files in a Git repo fixes that: [GNU Stow](https://www.gnu.org/software/stow/) symlinks them to where your system expects them, so a new machine only needs a clone and one command.

## What are dotfiles?

Dotfiles are config files whose names start with a period, like `.bashrc`, `.zshrc`, `.gitconfig`, and `.vimrc`. They hold the settings for your shell, editor, Git, and other tools.

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

### 2. Create the repo

```bash
mkdir ~/.dotfiles
cd ~/.dotfiles
git init
```

Keep this repo private when you push it. Config files often contain paths and usernames you don't want public. A private repo is still not a safe place for secrets, though, so don't skip step 5.

### 3. Tell Stow to ignore Git files

Create a `.stow-local-ignore` file so Stow doesn't symlink your `.git` directory or `.gitignore`:

```bash
printf '.git\n.gitignore\n' > .stow-local-ignore
```

Without this, Stow would try to symlink the whole `.git` folder into your home directory, which breaks Git in both places.

### 4. Move your configs

```bash
mv ~/.bashrc ~/.dotfiles/
mv ~/.zshrc ~/.dotfiles/
mv ~/.gitconfig ~/.dotfiles/
```

### 5. Keep secrets out of the repo

This step is critical. Your dotfiles should never contain secrets such as API keys, tokens, or passwords, even in a private repo. Once a secret is committed it stays in the Git history, and nothing in this setup checks for that. It is up to you to make sure it never happens.

Put every secret in one dedicated file and tell Git to ignore it:

```bash
touch .secrets
chmod 600 .secrets
echo ".secrets" >> .gitignore
```

Then load it from your shell config (`.zshrc` or `.bashrc`):

```bash
[ -f ~/.secrets ] && source ~/.secrets
```

Anything sensitive, like `export GITHUB_TOKEN=...`, goes in `.secrets` and nowhere else. Before you commit, run `git status` and check that `.secrets` is not listed.

### 6. Symlink with Stow

```bash
stow .
```

Stow creates symlinks in `~` that point back to `~/.dotfiles`. Your tools find the files where they expect them, but the real files live in the repo.

### 7. Commit and push

```bash
git add .
git commit -m "Initial dotfiles"
git remote add origin git@github.com:yourusername/dotfiles.git
git push -u origin main
```

## Restoring on a new machine

```bash
cd ~
git clone git@github.com:yourusername/dotfiles.git ~/.dotfiles
cd ~/.dotfiles
stow .
```

The `.secrets` file is not in the repo, so create it again by hand on the new machine.

## Tracking installed software

On macOS, Homebrew can write a list of everything you have installed:

```bash
brew bundle dump --describe
```

Commit the resulting `Brewfile` to the repo, and run `brew bundle install` on the new machine to reinstall it all.

## Tips

- Use GitHub's no-reply email in `.gitconfig`.
- Don't track files that change constantly, like `.zsh_history`.
- Write an install script once the setup has more than a few steps.
- Note the steps in the repo's README so you don't have to work them out again.

## Resources

- [Dotfiles Community](https://dotfiles.github.io/)
- [Atlassian Dotfiles Tutorial](https://www.atlassian.com/git/tutorials/dotfiles)
- [Managing with GNU Stow and Git](https://dev.to/luxcih/dotfiles-managing-with-gnu-stow-and-git-5100)
- [Fireship Video](https://www.youtube.com/watch?v=r_MpUP6aKiQ&t=294s)
