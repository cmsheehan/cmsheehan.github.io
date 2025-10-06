---
author: ["cmsheehan"]
title: "Create a profile / blog site with Hugo on Github Pages"
date: "2025-10-05"
description: ""
tags: ["hugo", "github"]
ShowToc: true
TocOpen: true
---



```bash
brew install hugo
hugo new site cmsheehan.github.io --format yaml
cd cmsheehan.github.io
git init
git remote add origin git@github.com:cmsheehan/cmsheehan.github.io.git

git submodule add --depth=1 https://github.com/adityatelange/hugo-PaperMod.git themes/PaperMod
git submodule update --init --recursive # needed when you reclone your repo (submodules may not get cloned automatically)
git submodule update --remote --merge


echo 'theme: ["PaperMod"]' >> hugo.yaml

```

```
asdf
asdf
asdf
asdf
asdf
asdf
asdf
asdf
asdf
asdf
asdf
asdf
asdf
asdf
asdf
asdf
asdf
asdf
asdf
asdf
asdf
asdf
asdf
asdf
asdf
asdf
asdf
asdf
asdf
asdf
asdf
asdf
asdf
asdf
asdf
asdf
asdf
asdf
asdf
asdf
asdf
asdf
asdf
asdf
asdf
asdf
asdf
asdf
asdf
asdf
asdf
asdf
asdf
asdf
asdf
asdf
asdf
asdf
asdf
asdf
asdf
asdf
asdf
asdf
asdf
asdf
asdf
asdf
asdf
asdf
asdf
asdf
asdf
asdf
asdf
asdf
asdf
asdf
asdf
asdf
asdf
asdf
asdf
asdf
```