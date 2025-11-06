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

If you’re interested in building a static website with [Hugo](https://gohugo.io/), I highly recommend starting with [this excellent guide by Testing with Marie](https://www.testingwithmarie.com/posts/20241126-create-a-static-blog-with-hugo/).

In this post, I’ll share the exact steps and commands I used to create my own Hugo site using the **PaperMod** theme, along with a few simple customizations to make it feel more personal.

---

## Setup

### 1. Install Hugo

If you’re on macOS, you can install Hugo easily with Homebrew:

```bash
brew install hugo
```

### 2. Create Your Hugo Site

Create a new Hugo site named after your GitHub Pages URL (for example, `cmsheehan.github.io`).

```bash
hugo new site cmsheehan.github.io --format yaml
cd cmsheehan.github.io
```

Initialize Git and connect your new site to your GitHub repository:

```bash
git init
git remote add origin git@github.com:cmsheehan/cmsheehan.github.io.git
```

### 3. Add the PaperMod Theme

The **PaperMod** theme is a clean, minimal layout that works great for blogs. Add it as a Git submodule using the following commands:

```bash
git submodule add --depth=1 https://github.com/adityatelange/hugo-PaperMod.git themes/PaperMod
git submodule update --init --recursive
git submodule update --remote --merge
```

This setup ensures the theme is properly cloned and stays updated when you pull new changes.

### 4. Enable the Theme

Add PaperMod as your active theme by appending this line to your `hugo.yaml`:

```bash
echo 'theme: ["PaperMod"]' >> hugo.yaml
```

### 5. Start the Development Server

You can now preview your site locally:

```bash
hugo server
```

Visit the address shown in your terminal (usually [http://localhost:1313](http://localhost:1313)) to view your site.

---

## Deploying to GitHub Pages

You can set up your site to automatically build and publish with every push.

Follow the instructions from these resources:

* [Testing with Marie’s Hugo setup guide](https://www.testingwithmarie.com/posts/20241126-create-a-static-blog-with-hugo/)
* [Official Hugo GitHub Pages documentation](https://gohugo.io/host-and-deploy/host-on-github-pages/)

These explain how to configure a **GitHub Actions workflow** to deploy your site automatically.

---

## Customizing the Theme

I wanted my site to have a slightly different look from the default PaperMod design. PaperMod makes this simple by letting you include your own CSS overrides.

Here’s what I did:

1. Located the default theme variables in

   ```
   themes/PaperMod/assets/css/core/theme-vars.css
   ```
2. Created a new file for my custom styles at

   ```
   assets/css/extended/custom.css
   ```
3. Added my own style changes there.

Hugo automatically includes files from the `assets/css/extended` folder, which allows you to customize the appearance without editing the original theme files.

For reference, see the [PaperMod FAQ section on adding custom CSS](https://adityatelange.github.io/hugo-PaperMod/posts/papermod/papermod-faq/#bundling-custom-css-with-themes-assets).

