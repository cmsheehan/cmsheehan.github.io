---
author: ["cmsheehan"]
title: "Makeme: A Simple Makefile Template"
date: "2025-11-04"
description: ""
tags: ["Makefile", "platform", "template"]
ShowToc: false
TocOpen: false
cover:
  image: "https://www.gnu.org/graphics/heckert_gnu.transp.small.png"
---

[Makeme](https://github.com/cmsheehan/makeme) is a small Makefile template I use to run the usual tasks in a project.

## Why a Makefile?

Every project has its own commands for starting a dev server, running tests, and building. A Makefile puts them behind the same few names, so I don't have to remember them or dig through the README.

With this template, running `make` on its own lists what's available:

```bash
$ make
Available commands:
  make dev        Start development server
  make test       Run tests
  make build      Build for production
```

It works the same way in every project that has the file.

## Why not just or Task?

[just](https://github.com/casey/just) and [Task](https://taskfile.dev/) have nicer syntax and more features, and if you already use one, stick with it.

I use Make because it's already installed on most systems, so there's nothing extra to set up.

## Setup

Download the template into your project:

```bash
curl -O https://raw.githubusercontent.com/cmsheehan/makeme/main/Makefile
```

Then edit the commands to match your project.

If you want to do this often, add an alias to your `~/.zshrc` or `~/.bashrc`:

```bash
alias makeme="curl -O https://raw.githubusercontent.com/cmsheehan/makeme/main/Makefile"
```

Restart your shell, and `makeme` will drop the template into whatever directory you're in.
