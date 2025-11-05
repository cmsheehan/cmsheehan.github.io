---
author: ["cmsheehan"]
title: "Makeme: A Simple Makefile Template"
date: "2025-11-04"
description: ""
tags: ["Makefile", "platform", "template"]
ShowToc: true
TocOpen: false
---

[Makeme](https://github.com/cmsheehan/makeme) is a simple Makefile template for running common project tasks.

## The Problem

Different projects use different commands. Instead of remembering specific commands or constantly checking the README, a Makefile provides a consistent interface.

## Why Makefiles?

Type `make` in any project to see all available commands:

```bash
$ make
Available commands:
  make dev        Start development server
  make test       Run tests
  make build      Build for production
```

Same interface across all projects.

## Why Make Instead of Just or Task?

Tools like [just](https://github.com/casey/just) and [Task](https://taskfile.dev/) have better syntax and more features. Use them if you prefer.

Make's advantage: it's already installed on most systems. No additional setup required.

## Setup

Download the Makefile template:

```bash
curl -O https://raw.githubusercontent.com/cmsheehan/makeme/main/Makefile
```

Optional: Create an alias for convenience.

**Zsh:**
```bash
echo 'alias makeme="curl -O https://raw.githubusercontent.com/cmsheehan/makeme/main/Makefile"' >> ~/.zshrc
source ~/.zshrc
```

**Bash:**
```bash
echo 'alias makeme="curl -O https://raw.githubusercontent.com/cmsheehan/makeme/main/Makefile"' >> ~/.bashrc
source ~/.bashrc
```

Then run `makeme` in any project directory and customize as needed.

## Summary

A simple template for consistent task running across projects. Available at [github.com/cmsheehan/makeme](https://github.com/cmsheehan/makeme).