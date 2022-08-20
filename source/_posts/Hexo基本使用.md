---
title: 'Hexo的基本使用'
date: 2020-05-29
tags: 
	- Hexo
---

### 1.前言
Hexo是一个快速、简洁且高效的博客框架。Hexo 使用 Markdown（或其他渲染引擎）解析文章，在几秒内，即可利用靓丽的主题生成静态网页。即把用户的markdown文件，按照指定的主题解析成静态网页。

### 2.安装hexo
安装使用hexo之前需要先安装Node.js和Git，当已经安装了Node.js和npm(npm是node.js的包管理工具)，可以通过以下命令安装hexo

```bash
npm install -g hexo-cli
```
可以通过以下命令查看主机中是否安装了node.js和npm

```bash
node --version    #检查是否安装了node.js
npm --version     #检查是否安装了npm
```
如果出现了版本号说明已经安装了node.js和npm。

### 3. 建站
安装完Hexo之后，执行下列命令，Hexo将会在指定目录中新建所需要的文件，指定的目录即为Hexo的工作站

```bash
hexo init <folder>
cd <folder>
npm install
```
新建完成之后，指定目录中的情况如下
```
.
├── _config.yml
├── package.json
├── scaffolds
├── source
|   ├── _drafts
|   └── _posts
└── themes
```
#### 3.1 _config.yml
网站的配置信息，您可以在此配置大部分的参数。[配置参数讲解](https://hexo.io/zh-cn/docs/configuration)

#### 3.2 scaffolds
模版文件夹。新建文章时，Hexo 会根据 `scaffold` 中的模板文件来建立新的文件。Hexo的模板是指在新建的markdown文件中默认填充的内容。例如，如果修改 `scaffold/post.md` 中的 `Front-matter` 内容，那么每次新建一篇文章时都会包含这个修改。也就是说，通过hexo命令每新建一个文章，都会包含指定模板文件中的内容。
[模板讲解](https://hexo.io/zh-cn/docs/templates)

#### 3.3 source
资源文件夹是存放用户资源的地方，如markdown文章。Markdown 和 HTML 文件会被解析并放到 public 文件夹，而其他文件会被拷贝过去。

#### 3.4 themes
主题文件夹。Hexo 会根据主题来解析source目录中的markdown文件生成静态页面。将下载的主题文件放到该文件夹下，并在配置文件下修改应用主题即可更换主题。[主题讲解](https://hexo.io/zh-cn/docs/themes)

### 4. 写作
可以执行下列命令来创建一篇新文章。

```bash
hexo new [layout] <title>
```
可以在命令中指定文章的布局（layout），不指定默认为 post，也可以通过修改 `_config.yml` 中的 `default_layout` 参数来指定默认布局。创建的新文章会自动加上指定布局对应的模板文件中的内容。

#### 4.1 布局（Layout）
Hexo 有三种默认布局：post、page 和 draft，它们分别对应不同的路径，而自定义的其他布局和 post 相同，都将储存到 `source/_posts` 文件夹。
布局路径 `postsource/_postspagesourcedraftsource/_drafts`
如果你不想你的文章被处理，你可以将 `Front-Matter` 中的 `layout` 设为 `false` 。

#### 4.2 模版（Scaffold）
在新建文章时，Hexo 会根据 scaffolds 文件夹内相对应的文件来建立文件，例如：

```bash
hexo new photo "My Gallery"
```
在执行这行指令时，Hexo 会尝试在 scaffolds 文件夹中寻找 `photo.md`，并根据其内容建立文章，以下是您可以在模版中使用的变量：

变量描述：
 - `layout` 布局
 - `title` 标题
 - `date` 文件建立日期

#### 4.3 Front-matter
Front-matter是文件最上方以 — 分隔的区域，用于指定个别文件的变量，举例来说：
```js
---
title: Hello World
date: 2020/5/13 20:46:25
---
```
注意：一般Front-matter使用的yaml语法，yaml语法需要注意空格，如`title: Hello World`冒号需要有一个空格，当然除YAML 外，你也可以使用 JSON 来编写 Front-matter。

以下是`Matery主题`预先定义的参数，您可在模板中使用这些参数值并加以利用。

|配置选项    |默认值	                  |描述|
|:-|:-|:-|
|title	    |`Markdown` 的文件标题         |  文章标题，强烈建议填写此选项|
|date	    |文件创建时的日期时间         |  发布时间，强烈建议填写此选项，且最好保证全局唯一|
|author 	|根 `_config.yml` 中的 `author`  |	文章作者|
|img	    |`featureImages` 中的某个值    |	文章特征图，推荐使用图床(腾讯云、七牛云、又拍云等)来做图片的路径.如: `http://xxx.com/xxx.jpg`|
|top	    |`true`                       |	推荐文章（文章是否置顶），如果 `top` 值为 `true`，则会作为首页推荐文章|
|cover	    |`false`                      |	`v1.0.2`版本新增，表示该文章是否需要加入到首页轮播封面中|
|coverImg	|无                         |	`v1.0.2`版本新增，表示该文章在首页轮播封面需要显示的图片路径，如果没有，则默认使用文章的特色图片|
|password	|无                         |	文章阅读密码，如果要对文章设置阅读验证密码的话，就可以设置 `password` 的值，该值必须是用 `SHA256` 加密后的密码，防止被他人识破。前提是在主题的 `config.yml` 中激活了 `verifyPassword` 选项|
|toc	    |`true`                       |	是否开启 `TOC`，可以针对某篇文章单独关闭 `TOC` 的功能。前提是在主题的 `config.yml` 中激活了 `toc` 选项|
|mathjax    |`false`                      |	是否开启数学公式支持 ，本文章是否开启 mathjax，且需要在主题的 `_config.yml` 文件中也需要开启才行|
|summary    |无                         |	文章摘要，自定义的文章摘要内容，如果这个属性有值，文章卡片摘要就显示这段文字，否则程序会自动截取文章的部分内容作为摘要|
|categories |无                         |	文章分类，本主题的分类表示宏观上大的分类，只建议一篇文章一个分类|
|tags	    |无                         |	文章标签，一篇文章可以多个标签|


##### 分类和标签
只有文章支持分类和标签，您可以在 Front-matter 中设置。在其他系统中，分类和标签听起来很接近，但是在 Hexo 中两者有着明显的差别：分类具有顺序性和层次性而标签没有顺序和层次。
```js
categories:
    - Diary
tags:
    - PS3
    - Games
```

#### 4.4 文章摘要
设置文章摘要，我们只需在想显示为摘要的内容之后添 <!-- more -->即可。像下面这样：

```js
---
title: hello hexo markdown
tags:
    - hexo
    - markdown
---
文章摘要

<!-- more -->

正文内容
```
这样，`<!-- more -->` 之前、文档配置参数之后中的内容便会被渲染为站点中的文章摘要。

注意！文章摘要在文章详情页是正文中最前面的内容。

#### 4.5 资源引用
写个博客，有时候会想添加个图片或者其他形式的资源等等。有以下两种方式进行解决：

 - 使用绝对路径引用资源，在 Web 世界中就是资源的 URL
 - 使用相对路径引用资源
对于使用相对路径引用资源的，我们可以使用 Hexo 提供的资源文件夹功能。

使用文本编辑器打开站点根目录下的 `_config.yml` 文件，将 post_asset_folder 值设置为 true。
```js
post_asset_folder: true
```
修改之后会开启 Hexo 的文章资源文件管理功能。Hexo 将会在我们每一次通过 `hexo new <title>` 命令创建新文章时自动创建一个同名文件夹，于是我们便可以将文章所引用的相关资源放到这个同名文件夹下，然后通过相对路径引用。例如，你把一个 example.jpg 图片放在了这个同名文件夹中，使用相对路径的常规 markdown 语法 `![](./example.jpg)`即可访问 。

### 5. 网站发布
首先执行下列命令生成相应的静态网页，生成的静态网页以及相关资源都会在public目录下

```bash
hexo generate       #可简写成 hexo g
```
#### 5.1 用hexo-server
hexo-server模块的主要命令如下，输入以下命令以启动服务器，您的网站会在本地启动。在服务器启动期间，Hexo 会监视文件变动并自动更新，您无须重启服务器。

```bash
hexo server     #可简写成 hexo s
hexo server -p 5000     #指定端口
```
由于是本地启动，比较适用于调试。

#### 5.2 部署到Git上
在项目根目录下找到配置文件 `_congif.yml`，找到 deploy 字段并填写完整

```yml
# Deployment
## Docs: https://hexo.io/docs/deployment.html
deploy:
  type: git
  repo: <你的仓库地址> #git@github.com:Feng-ML/Feng-ML.github.io.git
  branch: master
  ignore_hidden: false
```
并且我们需要额外的一个工具来帮助我们推到仓库上

```bash
npm install hexo-deployer-git --save
```
然后执行以下命令即可自动部署到github上

```bash
hexo clean
hexo deploy
```
#### 5.3 部署到Apache或者Nginx上
通过hexo g命令生成的都是静态网页，可以把生成的public目录中的文件，全都拷贝到网站根目录，然后启动apache或者nginx服务。

### 6.参考链接
[Hexo](https://hexo.io/zh-cn/docs/)
主题：[Matery](https://github.com/blinkfox/hexo-theme-matery/blob/develop/README_CN.md)