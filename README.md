
brew install hugo
hugo new site cmsheehan.github.io --format yaml
cd cmsheehan.github.io
git init
git remote add origin git@github.com:cmsheehan/cmsheehan.github.io.git

git submodule add --depth=1 https://github.com/adityatelange/hugo-PaperMod.git themes/PaperMod
git submodule update --init --recursive # needed when you reclone your repo (submodules may not get cloned automatically)
git submodule update --remote --merge


echo 'theme: ["PaperMod"]' >> hugo.yaml

- make customizations, link to exmaple

mkdir -p .github/workflows
touch .github/workflows/hugo.yaml


### References:

- https://www.testingwithmarie.com/posts/20241126-create-a-static-blog-with-hugo/
- https://github.com/adityatelange/hugo-PaperMod/tree/exampleSite/
- https://gohugo.io/host-and-deploy/host-on-github-pages/