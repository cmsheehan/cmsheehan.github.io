# Hugo Blog Setup

A static blog built with Hugo and the PaperMod theme, deployed to GitHub Pages.

## Setup

### Install Hugo
```bash
brew install hugo
```

### Create New Site
```bash
hugo new site cmsheehan.github.io --format yaml
cd cmsheehan.github.io
```

### Initialize Git
```bash
git init
git remote add origin git@github.com:cmsheehan/cmsheehan.github.io.git
```

### Add PaperMod Theme
```bash
git submodule add --depth=1 https://github.com/adityatelange/hugo-PaperMod.git themes/PaperMod
git submodule update --init --recursive
```

### Configure Theme
```bash
echo 'theme: ["PaperMod"]' >> hugo.yaml
```

### Customize Configuration
See [PaperMod example site](https://github.com/adityatelange/hugo-PaperMod/tree/exampleSite/) for configuration options.

### Setup GitHub Actions
```bash
mkdir -p .github/workflows
touch .github/workflows/hugo.yaml
```

Add the GitHub Actions workflow for automated deployment (see [Hugo documentation](https://gohugo.io/host-and-deploy/host-on-github-pages/)).

## When Cloning the Repository

Update submodules after cloning:
```bash
git submodule update --init --recursive
git submodule update --remote --merge
```

## References

- [Create a Static Blog with Hugo](https://www.testingwithmarie.com/posts/20241126-create-a-static-blog-with-hugo/)
- [PaperMod Example Site](https://github.com/adityatelange/hugo-PaperMod/tree/exampleSite/)
- [Hugo GitHub Pages Deployment](https://gohugo.io/host-and-deploy/host-on-github-pages/)