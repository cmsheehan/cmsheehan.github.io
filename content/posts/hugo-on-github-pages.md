---
author: ["cmsheehan"]
title: "Creating a Hugo Blog with the PaperMod Theme"
date: "2025-10-05"
description: ""
tags: ["hugo", "github", "how-to"]
ShowToc: false
TocOpen: false
cover:
   image: "https://gohugo.io/images/hugo-logo-wide.svg"
---

If you want to build a static site with [Hugo](https://gohugo.io/), start with [this guide by Testing with Marie](https://www.testingwithmarie.com/posts/20241126-create-a-static-blog-with-hugo/).

This post lists the steps and commands I used to set up my own site with the **PaperMod** theme, plus a few small customizations.

## Setup

### 1. Install Hugo

On macOS, install Hugo with Homebrew:

```bash
brew install hugo
```

### 2. Create the site

Name the site after your GitHub Pages URL (mine is `cmsheehan.github.io`):

```bash
hugo new site cmsheehan.github.io --format yaml
cd cmsheehan.github.io
```

Initialize Git and point it at your GitHub repository:

```bash
git init
git remote add origin git@github.com:cmsheehan/cmsheehan.github.io.git
```

### 3. Add the PaperMod theme

PaperMod is a clean, minimal theme that works well for blogs. Add it as a Git submodule:

```bash
git submodule add --depth=1 https://github.com/adityatelange/hugo-PaperMod.git themes/PaperMod
git submodule update --init --recursive
git submodule update --remote --merge
```

The first command adds the theme, and the other two make sure it is fully cloned and up to date.

### 4. Enable the theme

Set PaperMod as the active theme by adding this line to `hugo.yaml`:

```bash
echo 'theme: ["PaperMod"]' >> hugo.yaml
```

### 5. Start the development server

Preview the site locally:

```bash
hugo server
```

Open the address shown in your terminal, usually [http://localhost:1313](http://localhost:1313).

## Deploying to GitHub Pages

To build and publish the site on every push, set up a GitHub Actions workflow. Both of these explain how:

* [Testing with Marie’s Hugo setup guide](https://www.testingwithmarie.com/posts/20241126-create-a-static-blog-with-hugo/)
* [Official Hugo GitHub Pages documentation](https://gohugo.io/host-and-deploy/host-on-github-pages/)

## Customizing the theme

I wanted my site to look a little different from the default PaperMod design. PaperMod lets you add your own CSS overrides, so I:

1. Looked up the default theme variables in

   ```
   themes/PaperMod/assets/css/core/theme-vars.css
   ```
2. Created a file for my own styles at

   ```
   assets/css/extended/custom.css
   ```
3. Put my style changes there.

Hugo picks up any file in `assets/css/extended` automatically, so you can change how the site looks without editing the theme's own files.

For more detail, see the [PaperMod FAQ section on adding custom CSS](https://adityatelange.github.io/hugo-PaperMod/posts/papermod/papermod-faq/#bundling-custom-css-with-themes-assets).
