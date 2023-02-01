# mdBook构建静态网站模板

<https://github.com/Yang-Xijie/mdbook-site>

- 使用教程 [mdbook-site](https://yang-xijie.github.io/BLOG/Markdown/mdbook-site/)

---

- 生成 [mdBook](https://rust-lang.github.io/mdBook/)
- 发布 [GitHub Pages](https://yang-xijie.github.io/BLOG/Markdown/github-pages/) 

---

个人更推荐 Material for MkDocs 一些，之前也用过 mdBook，但是...逐渐不能忍受 mdBook 的原因：

- 修改文章保存后在浏览器实时预览的功能有bug
- 不支持使用`$`符号来写数学公式
- 上面的导航栏特别大 而且突然出现 多次误点在正在看文章的时候跳转到页面顶部
- 不支持可交互的目录
- 随着文章增多 左侧的边栏变得越来越长 找文章很难找

Material for MkDocs 则真的很现代 开箱即用 可以配置的选项超级多

总之我是非常安利 使用指南 <https://github.com/Yang-Xijie/mkdocs-site>


---
# Publish a Website with mdBook and GitHub Pages

template at <https://github.com/Yang-Xijie/mdbook-site>

## mdbook init

```
$ mkdir mdbook-site
$ cd mdbook-site
$ mdbook init

Do you want a .gitignore to be created? (y/n)
y
What title would you like to give the book?
my-book
2022-05-21 17:50:25 [INFO] (mdbook::book::init): Creating a new book with stub content

All done, no errors...
```

```
$ tree -a
.
├── .gitignore
├── book
├── book.toml
└── src
    ├── SUMMARY.md
    └── chapter_1.md
```
* .gitignore 创建的忽略配置文件
* book 初始化项目结构自带的文件夹，里面放mdbook解析md文件后的html页面（可放在新的分支）
* book.toml 作者、语言、源目录、标题
* src 源目录
  * SUMMARY.md 指定项目结构（填写标题和标题的md路径）
  * chapter_1.md 初始化项目结构自带md（测试）
  
## Add GitHub Workflow

```
$ mkdir .github
$ cd .github
$ mkdir workflows
$ cd workflows
$ vim PublishMySite.yml
```

```yml
name: PublishMySite

# Controls when the action will run. 
on:
  # Triggers the workflow on push or pull request events but only for the main branch
  push:
    branches:
      - main
  pull_request:
    branches:
      - main

# A workflow run is made up of one or more jobs that can run sequentially or in parallel
jobs:
  # This workflow contains a single job called "build"
  build:
    # The type of runner that the job will run on
    runs-on: ubuntu-latest

    # Steps represent a sequence of tasks that will be executed as part of the job
    steps:
      # Checks-out your repository under $GITHUB_WORKSPACE, so your job can access it
      - uses: actions/checkout@v2

      # Build markdown files to a static site.
      - name: Setup mdBook
        uses: peaceiris/actions-mdbook@v1
        with:
          mdbook-version: "latest"
      - run: mdbook build . --dest-dir ./book # --dest-dir is relative to <dir>
      
      # Publish the static site to gh-pages branch.
      - name: Deploy
        uses: peaceiris/actions-gh-pages@v3
        with:
          github_token: ${{ secrets.GITHUB_TOKEN}}
          publish_dir: ./book
          publish_branch: gh-pages
```

```
$ tree -a
.
├── .github
│   └── workflows
│       └── PublishMySite.yml
├── .gitignore
├── book.toml
└── src
    ├── SUMMARY.md
    └── chapter_1.md
```

## Git and GitHub

### git init

```
$ git init
$ git add .
$ git commit -m "init"
```

### GitHub - New Repository

GitHub > New Repository

GitHub > Repository > Settings > Actions > General > 

- Actions permissions: Allow all actions and reusable workflows
- Workflow permissions: Read and write permissions
- Click Save

```
$ git remote add origin git@github.com:Yang-Xijie/mdbook-site.git # change to your github repo
$ git branch -M main
$ git push -u origin main
```

GitHub > Repository > Settings > Pages > Source > gh-pages > Click Save

## Postscript

View your site at <https://yang-xijie.github.io/mdbook-site/>.

Modify your markdown files in src/, make a new commit and push your site to GitHub. GitHub will automatically publish the newest contents.
