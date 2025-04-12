---
title: Use Hexo
date: 2022-12-31 00:00:00
tags: hexo
categories: tech
---

---

## 全局安装 hexo-cli

`cnpm install -g hexo-cli`

## 初始化博客

`hexo init`

默认主题 landscape

## 启动

`hexo s`

## 新建一篇文章

`hexo n "use hexo step by step"`

## 新建完成后

`hexo clean`

`hexo g`

再重新启动检验

## 部署到 GitHub
1. 新建一个git项目, 注意项目名必须是 `your_git_hub_name.github.io`; 例如 `perryyao-github.github.io`
2. 在本地创建 git 仓库, 但是不太需要 push, 因为 `hexo d` 本质上就是 push
    ```bash
    git remote add origin git@github.com:PerryYao-GitHub/perryyao-github.github.io.git
    git branch -M main
    git push -u origin main
    ```
3. 安装 `npm install --save hexo-deployer-git`
4. 在 `_config.yml`中做配置
   ```yaml
   # Deployment
   ## Docs: https://hexo.io/docs/one-command-deployment
   deploy:
     type: 'git'
     repo: https://github.com/PerryYao-GitHub/perryyao-github.github.io
     branch: main
   ```
5. 执行 `hexo d`, 输入git账户密码

## 至此以后写博客就是几个步骤:
1. `hexo n "blog-name"`
2. `hexo clean`
3. `hexo g`
4. `hexo s` (查看效果)
5. `hexo d`

其中 2, 3 是修改了hexo项目后, 想要部署之前必做

## 部署 NexT theme

### 下载 Next 主题：

使用 Git 下载 Next 主题到 themes/ 目录中 (一定要位于项目的根路径下执行)
```bash
git clone https://github.com/theme-next/hexo-theme-next themes/next
```

### 切换主题
`-config.yml` 中, `theme: next`

### 配置主题
打开 themes/next/_config.yml 文件，修改以下基本配置:
```yaml
title: My Blog
author: Your Name
description: A place to share knowledge.
```

### 安装必要依赖
`npm install hexo-renderer-pug hexo-renderer-stylus --save`

### 开启标签, 分类功能
**新建 标签 和分类 页面**   
在根目录输入命令 `hexo new page categories` 会自动新建 `categories` 文件夹并生成一个`index.md`文件，将里面的代码改为：

```
---
title: categories
date: 2020-01-21 12:12:26
type: "categories" 
---
```
同理，「标签」也一样 `hexo new page tags` 生成 `tags` 文件夹，其中会自动生成一个 `index.md` 文件，将代码改为：

```
---
title: tags
date: 2020-01-21 12:13:08
type: "tags"
---
```
**配置菜单**   
在主题文件下的_config.yml中

```
menu:
  home: / || home
  #about: /about/ || user
  tags: /tags/ || tags
  categories: /categories/ || th
  archives: /archives/ || archive
  #schedule: /schedule/ || calendar
  #sitemap: /sitemap.xml || sitemap
  #commonweal: /404/ || heartbeat

# Enable / Disable menu icons / item badges.
menu_settings:
  icons: true
```

**使用时**   
如果要对文章使用「tags」「categories」，只需在文章开头添加如下代码：

```
---
title: 摸鱼
date: 2020-01-21 12:04:59
tags: 杂项
categories: 技术
---
```
为了方便，我们可以修改post模板，修改`Hexo\scaffolds\post.md`

```
---
title: {{ title }}
date: {{ date }}
tags:
categories:
---
```
