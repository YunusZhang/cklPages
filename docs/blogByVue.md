# 使用 github-pages+vue 搭建个人博客
## 1 使用gp快速部署静态网页
### 1.1 前言
在日常工作中, 我们经常会遇到要做 demo 展示的情况. 做 demo 展示不同于做项目开发, 我们需要的是快速轻便的开发和部署, 而不是完备的一整套开发流程.

下图肯定不是我们做一个 demo 想要的流程.

尤其对于数据可视化工作, 能快速的创建一个 demo 来验证自己的想法, 并且方便的和同伴分享自己作品是非常重要的.

在这里给大家介绍一种笔者经常用来做 demo 的方法: 

Github Pages.

### 1.2 选择 github pages 的理由

使用零成本: github pages 集成在 github 中, 直接和代码管理绑定在一起, 随着代码更新自动重新部署, 使用非常
方便.

免费: 免费提供 username.github.io 的域名, 免费的静
态网站服务器.

无数量限制: github pages 没有使用的数量限制, 每一个 github repository 都可以部署为一个静态网站.

### 1.3 使用方法
#### 1.3.1 一. 部署静态网页  

首先我们介绍一下部署最基础的静态网页, 最终的效果是展示出一个 Hello, github pages :) 页面.

demo 地址: github repository 

github page 地址: ssthouse.github.io/
 
github-page…


1.1 创建一个 github 项目

前往 github 官网, 点击 New repository 创建新项目. 填入项目基本信息, 点击 Create Repository 确认.

确认完成后会看到如下页面:

1.2 为 repository 开启 github page 选项

如图, 我们选中 Setting tab

往下滚动, 找到 Github Pages 选项, 将 Source 改为 master branch, 最后点击 Save 按钮

最后我们会得到一个链接, 通过这个链接, 待会我们就能通过这个链接访问到该项目的 github pages 页面.

1.3 代码 clone 到本地, 并创建几个基本文件

1.4 添加基础代码

注意这里 html 因为和 css 以及 js 放在同一目录, 所以我们用相对路径来引用.

代码逻辑很简单, 就是在页面加载好后在页面中添加 Hello, github pages :) 这段文字.

index.html
```html
<!DOCTYPE html>
<html>
<head>
    <meta charset="utf-8" />
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <title>Github Page demo</title>
    <meta name="viewport" content="width=device-width, initial-scale=1">
    <link rel="stylesheet" type="text/css" media="screen" href="main.css" />
    <script src="index.js"></script>
</head>
<body>
    <div id="main-content">

    </div>
</body>
</html>

```

复制代码index.js

```js
window.onload = function() {
  document.getElementById('main-content').innerHTML = 'Hello, github pages :)'
}

```
复制代码main.css

```css
#main-content {
  font-size: 36px;
  font-weight: bold;
  padding: 16px;
}
```



1.5 将代码更新到 github 仓库
```
    cd github-pages-demo
    git add .
    git commit -m "Add simple code"
    git push
```

1.6 看看效果

现在我们访问之前开启 github pages 选项时获得的 url, 就可以看到效果了

注: 代码 push 上去后, 可能要过几分钟才会部署好生效

```
效果如图 :arrow_down:
```

#### 1.3.2 二. 使用前端框架时, 如何使用 github pages

如今我们创建一个前端项目的时候, 已经很少手动创建 index.html, main.js , main.css 这文件了, 通常我们都会选择一个前端框架, 并使用相应的 command line tool 来初始化项目.

这里笔者用 Vue 的 webpack 项目 做介绍, rect 和 angular 进行类似的配置即可.

##### 创建项目

首先我们用 vue-cli 创建一个 webpack 管理的 vue 项目 (点我安装 vue-cli),

vue init webpack github-page-vue-demo
复制代码然后我们进入项目, 看看目录结构:

可以看到 config 目录中有三个文件:
```
config                     //  配置目录
├── dev.env.js             // 用于development模式的环境变量
├── index.js               // 用于配置 `dev` 模式和 `prod` 模式的 webpack config 文件
└── prod.env.js            // 用于product模式的
环境变量
```
复制代码这里我们需要配置的就是 index.js 文件, 先看看该文件内容 (这里将不相关的代码用...略过), 其中我们需要关注的是 module.exports 的 build 属性, 我们将在这里配置 webpack build 时生成文件的路径

```js
module.exports = {
  dev: {
      ...
  },

  build: {
    // Template for index.html
    index: path.resolve(__dirname, '../dist/index.html'),

    // Paths
    assetsRoot: path.resolve(__dirname, '../dist'),
    assetsSubDirectory: 'static',
    assetsPublicPath: '/',
    ...
  }
}

```

复制代码可以看到图中主要配置了 index 文件和 assets 文件的路径. 默认执行 yarn run build 后 webpack 会将项目打包到项目根目录的 ./dist 文件夹, 如图:

##### 修改编译配置

但是 github pages 默认只能识别项目根目录的 index 文件, 如果我们想要让 github pages 识别到我们 build 出来的文件应该怎么办呢?

你可能会想到直接将 dist 文件夹中 build 生成的文件直接复制到项目的根目录, 这确实是个办法. 但是这样的话, 我们每次 build 完, 都需要手动复制一边文件, 这无疑增加了很多重复性的工作.

我们可以通过修改默认的配置来达到项目 build 的文件直接生成到项目根目录的目的, 像这样:

```js
module.exports = {
  dev: {
      ...
  },

  build: {
    // Template for index.html
    index: path.resolve(__dirname, '../index.html'),  //之前是 '../dist/index.html'

    // Paths
    assetsRoot: path.resolve(__dirname, '../'),  // 之前是 '../dist'
    assetsSubDirectory: 'static',
    assetsPublicPath: './',    // 之前是 '/'
    ...
  }
}

```

复制代码所做的改动就是去掉了默认的 dist 目录, 并且将 assets 的引用路径从 绝对路径 改为了 相对路径.

去掉 dist 目录是为了将 build 的 target 路径改为项目根目录. 改为相对路径是因为在部署到 github pages 的时候, 我们的域名是 https://username.github.io/repositoryName, 也就是说我们的项目是部署在子域名上的, 如果用绝对路径会导致 assets 文件 404.

这样修改完后我们又发现一个问题: 在这样的配置下, build 结束生成的 index.html 文件会覆盖原有的 template index.html 文件, 并且根目录多了一个 static 文件夹, 很容易让人对这个文件夹的作用产生疑惑. 有没有更好的解决办法呢 ?

让我们回到 github page 的 setting 页面:

可以看到这里有一个选项是 master branch /docs folder . 当前状态是不可选的, 原因是我们的项目代码里面没有 /docs 目录.

这个选项的意思是 github page 可以识别我们项目中的 docs 文件夹, 并在这个文件夹中寻找 index 文件进行部署. 选中这个选项后, 我们只需要将之前 webpack 默认的 dist 文件夹改为 docs 文件夹即可, 修改后配置如下:

```js
module.exports = {
  dev: {
      ...
  },

  build: {
    // Template for index.html
    index: path.resolve(__dirname, '../docs/index.html'),  //之前是'../dist/index.html'

    // Paths
    assetsRoot: path.resolve(__dirname, '../docs'),  // 之前是 '../dist'
    assetsSubDirectory: 'static',
    assetsPublicPath: './',    // 之前是 '/'
    ...
  }
}

```

复制代码完成以上的修改后, 我们再次运行 yarn run build, 你会发现根目录下多了一个 docs 文件夹, 里面承载了 index 文件和 static 文件夹. 我们讲 docs 目录以及其下的文件全部加入 git 版本管理, 并 push 到 github.

再次来到 该项目的 github page setting 页面, 这时 master branch /docs folder 就变成可选中的了. 我们选中这个选项, 并保存设置.

过两分钟左右, 我们再次访问我们项目的 github page url, 就会发现项目已经部署成功了 :tada:

##### 自定义 template

在上一步中, 我们自己配置了一个 基于 Vue + Webpack 的项目配置. 但如果每次我们想创建一个 demo, 我们都要修改一遍配置的话, 还是很花费时间. 特别是忘记了配置步骤的话, 还得再查找之前的配置方法.

一个比较好的解决方案是创建一个基础的 template repository, 在下次需要创建一个 demo 项目的时候, 直接 fork 过来, 基于这个项目着手开发即可.

比如我经常使用的技术栈是: Vue + D3.js + Webpack, 我就给自己创建了一个这样的 template, 并做好 github page 的配置. 下次要做数据可视化 demo 的时候, 直接 fork 这个 repository 进行开发, 节省了许多项目配置的时间, 如果有和我类似技术栈的小伙伴, 不妨一试:

Vue + D3.js + Webpack (github page ready)
github 地址

#### 1.3.3 三. 只可以是静态网站吗?
github page 可以非常方便的部署静态网页. 但是因为没有数据库, 无法存储数据, 也就没办法实现较为复杂的业务逻辑. 但是 github page 真的就只能做做简单的纯静态网站了吗?

其实, 如今前后端分离, 只要有后端 api, 前端完全可以单独开发和部署.

如果我们的网站已有现成的后端 api, 使用 github page, 再加上 javascript 调用后端 api, 完全可以实现所有的业务逻辑.

如果没有现有后端 api, 但是想要实现简单的后端逻辑的话, 这里推荐一个笔者常用的小 tip:

使用 leancloud 作为对象存储的后端数据库
前端调用 leancloud api 实现所有的业务逻辑

如果感兴趣不妨看看我的这个项目: github visualization

这个项目就是部署在 github pages 上, 并使用 leancloud api 实现的 用户访问信息记录 的功能.
具体的 leancloud 使用细节, 请参见 leancloud 文档

最后
github pages 可以说是为我节省了许多的时间, 为我开发 demo 展示提供了非常多的便利. 推荐没有使用过的小伙伴试试, 相信也会给你们带来很多便利.

作者：ssthouse
链接：https://juejin.im/post/5b30babff265da59594695ab
来源：掘金


## 2 遇到的坑
### 2.1 vue-cli安装
前言

Vue作为前端三大框架（Angular，React，Vue）之一，号称是最简单，最容易上手的框架，同时也是行内的大趋势，还可以用来开发最火的小程序。具有开发快，双向数据流等特点，有些人认为Vue是Angular和React的结合，既有Angular的模板语法也有React的组件化体系，以至于促使其发展飞快。

下面来就来讲讲Vue脚手架（vue-cli）的安装吧。

图片描述

安装vue（使用npm 推荐）

```
# 全局安装 vue-cli
$ npm install --global vue-cli
# 创建一个基于 webpack 模板的新项目
$ vue init webpack my-project
# 安装依赖，走你
$ cd my-project
$ npm install
$ npm run dev

```

当使用npm install --global vue-cli命令时报：npm: command not found的解决办法。

1、重装nodejs。

2、依次执行下面的命令：

```
sudo apt-get remove npm
sudo apt-get remove nodejs-legacy
sudo apt-get remove nodejs 
sudo rm /usr/bin/node 
sudo apt-get install nodejs 
sudo apt-get install nodejs-legacy 
sudo apt-get install npm
```

执行上面命令的时候会出现：sudo: apt-get: command not found，原因是apt-get是debian(Ubuntu)才有的包管理器。

1、解决办法一：使用brew代替apt-get

What is brew?

brew 全称Homebrew，是Mac OSX上的软件包管理工具，能在Mac中方便的安装软件或者卸载软件。

如何安装？

安装只需要在终端下输入下面一条命令（Mac自带ruby不需要安装）
```
ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
```

安装完成后使用brew install libxml2即可。如需查看brew的更多功能请使用brew --help查看其帮助文档。

2、解决办法二：Fink 安装过程这里就不再阐述，有兴趣的可以去看看，传送门Fink的安装

brew安装好之后就可以依次执行下面的命令：

```
brew remove npm
brew remove nodejs-legacy
brew remove nodejs
brew rm /usr/bin/node
brew install nodejs
brew install nodejs-legacy
brew install npm
```

继续执行npm install --global vue-cli安装架手架，发现npm不能安装模块。原因是proxy的问题，要求node版本是node-6.2.1。

执行下面命令：

```
npm config rm proxy
npm config rm https-proxy
```

Mac中在/Users/YourUserName下找不到.bash_profile则自己创建.bash_profile文件。运行下面命令：

```
cd /Users/YourUserName //进入文件目录位置
touch .bash_profile   //创建文件
open －e .bash_profile //打开并编辑（文件可为空）
source .bash_profile  //编辑保存了执行这句编译下
```

上面步骤操作完了，再执行：npm install --global vue-cli，现在可以执行了。

当执行vue init webpack my-project时如果报：bash vue :command not found，这个提示是指没有成功的安装vue-cli，重新执行npm install --global vue-cli全局安装就可以了。

上面讲的是用npm安装，当然你也可以用yarn。想用yarn安装，就必须用npm安装一下yarn。执行命令：npm install -g yarn

修改全局路径为默认路径：

```
npm root -g //查看本机全局的npm包的安装路径
npm config set prefix /usr/local //修改全局路径为默认路径
npm -v //可以看到nodejs相应的版本号
```

创建新项目
```
cd /Users/app/Downloads/project
vue init webpack my-project(项目名字)  //创建新项目

//创建项目之前会询问你项目相关信息，信息如下：

? Project name my-project    //设置项目名称
? Project description my-project app  //设置项目描述
? Author Martyr    //设置作者
? Vue build standalone    //使用标准的文件或是压缩过的文件（标准）
? Install vue-router? Yes    //是否安装vue-router（路由）
? Use ESLint to lint your code? No    //是否使用语法检查器（检查十分严格）
? Pick an ESLint preset Standard    //用何种（选择标准即可）
? Set up unit tests NO    //是否使用单元测试
? Setup e2e tests with Nightwatch? No    //是否使用e2e测试
? Should we run `npm install` for you after the project has been cre
ated? (recommended) npm    //是否需要现在通过npm或yarn帮你配置项目，或是稍后你自己动手安装
安装完成后出现运行项目的相关提示：
cd my-project    //进入my-project文件夹
npm install //安装依赖；
npm run dev //运行项目

```

最后，浏览器地址栏输入：http://localhost:8080回车，新建的vue-cli模板项目就运行了。

<div style="color:red">
注意：npm run dev //运行项目
这条命令只是测试框架有没有安装成功

要打包部署还需要命令
npm run build

详见参考

https://github.com/ssthouse/vue-d3-template

</div>


附：

在项目里安装Vux移动端
```
npm install vux --save
npm install vux-loader --save-dev //安装vux 必
```
须安装vux-loader 否则报错

安装weex开发APP

//全局安装 weex-toolkit 在app下执行就可以
```
sudo npm install -g weex-toolkit //安装
appdeMacBook-Pro: app$ weex -v //查看版本号，出
```
现如下显示则安装成功
```
   v1.3.11
 - weex-builder : v0.4.0
 - weex-previewer : v1.5.1
 - 
```
相关资料

创建Vue项目 以及引入Iview
npm: command not found的解决方案
Mac OSX下使用apt-get命令
vue新建项目（-）vue-cli安装
Mac 打开、编辑 .bash_profile 文件

## 3 相关git操作报错

### 3.1 报错1 Updates were rejected because the tip of your current branch is behind

当你从远程仓库克隆时，实际上Git自动把本地的master分支和远程的master分支对应起来了，并且，远程仓库的默认名称是origin。

要查看远程库的信息，用git remote：
```
$ git remote
origin
```

或者，用git remote -v显示更详细的信息：

```
$ git remote -v
origin  git@github.com:michaelliao/learngit.git (fetch)
origin  git@github.com:michaelliao/learngit.git (push)
```

上面显示了可以抓取和推送的origin的地址。如果没有推送权限，就看不到push的地址。

推送分支

推送分支，就是把该分支上的所有本地提交推送到远程库。推送时，要指定本地分支，这样，Git就会把该分支推送到远程库对应的远程分支上：

```
$ git push origin master
```

如果要推送其他分支，比如dev，就改成：

```
$ git push origin dev
```

但是，并不是一定要把本地分支往远程推送，那么，哪些分支需要推送，哪些不需要呢？

master分支是主分支，因此要时刻与远程同步；

dev分支是开发分支，团队所有成员都需要在上面工作，所以也需要与远程同步；

bug分支只用于在本地修复bug，就没必要推到远程了，除非老板要看看你每周到底修复了几个bug；

feature分支是否推到远程，取决于你是否和你的小伙伴合作在上面开发。

总之，就是在Git中，分支完全可以在本地自己藏着玩，是否推送，视你的心情而定！



抓取分支

多人协作时，大家都会往master和dev分支上推送各自的修改。

现在，模拟一个你的小伙伴，可以在另一台电脑（注意要把SSH Key添加到GitHub）或者同一台电脑的另一个目录下克隆：

```
$ git clone git@github.com:michaelliao/learngit.git
Cloning into 'learngit'...
remote: Counting objects: 40, done.
remote: Compressing objects: 100% (21/21), done.
remote: Total 40 (delta 14), reused 40 (delta 14), pack-reused 0
Receiving objects: 100% (40/40), done.
Resolving deltas: 100% (14/14), done.
```

当你的小伙伴从远程库clone时，默认情况下，你的小伙伴只能看到本地的master分支。不信可以用git branch命令看看：

```
$ git branch
* master
```

现在，你的小伙伴要在dev分支上开发，就必须创建远程origin的dev分支到本地，于是他用这个命令创建本地dev分支：

```
$ git checkout -b dev origin/dev
```

现在，他就可以在dev上继续修改，然后，时不时地把dev分支push到远程：

```
$ git add env.txt

$ git commit -m "add env"
[dev 7a5e5dd] add env
 1 file changed, 1 insertion(+)
 create mode 100644 env.txt

$ git push origin dev
Counting objects: 3, done.
Delta compression using up to 4 threads.
Compressing objects: 100% (2/2), done.
Writing objects: 100% (3/3), 308 bytes | 308.00 KiB/s, done.
Total 3 (delta 0), reused 0 (delta 0)
To github.com:michaelliao/learngit.git
   f52c633..7a5e5dd  dev -> dev

```

你的小伙伴已经向origin/dev分支推送了他的提交，而碰巧你也对同样的文件作了修改，并试图推送：

```
$ cat env.txt
env

$ git add env.txt

$ git commit -m "add new env"
[dev 7bd91f1] add new env
 1 file changed, 1 insertion(+)
 create mode 100644 env.txt

$ git push origin dev
To github.com:michaelliao/learngit.git
 ! [rejected]        dev -> dev (non-fast-forward)
error: failed to push some refs to 'git@github.com:michaelliao/learngit.git'
hint: Updates were rejected because the tip of your current branch is behind
hint: its remote counterpart. Integrate the remote changes (e.g.
hint: 'git pull ...') before pushing again.
hint: See the 'Note about fast-forwards' in 'git push --help' for details.
```

推送失败，因为你的小伙伴的最新提交和你试图推送的提交有冲突，解决办法也很简单，Git已经提示我们，先用git pull把最新的提交从origin/dev抓下来，然后，在本地合并，解决冲突，再推送：

```
$ git pull
There is no tracking information for the current branch.
Please specify which branch you want to merge with.
See git-pull(1) for details.

    git pull <remote> <branch>

If you wish to set tracking information for this branch you can do so with:

    git branch --set-upstream-to=origin/<branch> dev
```

git pull也失败了，原因是没有指定本地dev分支与远程origin/dev分支的链接，根据提示，设置dev和origin/dev的链接：


```
$ git branch --set-upstream-to=origin/dev dev
Branch 'dev' set up to track remote branch 'dev' from 'origin'.
```

再pull：

```
$ git pull
Auto-merging env.txt
CONFLICT (add/add): Merge conflict in env.txt
Automatic merge failed; fix conflicts and then commit the result.

```

这回git pull成功，但是合并有冲突，需要手动解决，解决的方法和分支管理中的解决冲突完全一样。解决后，提交，再push：

```
$ git commit -m "fix env conflict"
[dev 57c53ab] fix env conflict

$ git push origin dev
Counting objects: 6, done.
Delta compression using up to 4 threads.
Compressing objects: 100% (4/4), done.
Writing objects: 100% (6/6), 621 bytes | 621.00 KiB/s, done.
Total 6 (delta 0), reused 0 (delta 0)
To github.com:michaelliao/learngit.git
   7a5e5dd..57c53ab  dev -> dev
```


```
因此，多人协作的工作模式通常是这样：

首先，可以试图用git push origin <branch-name>推送自己的修改；

如果推送失败，则因为远程分支比你的本地更新，需要先用git pull试图合并；

如果合并有冲突，则解决冲突，并在本地提交；

没有冲突或者解决掉冲突后，再用git push origin <branch-name>推送就能成功！

如果git pull提示no tracking information，则说明本地分支和远程分支的链接关系没有创建，用命令git branch --set-upstream-to <branch-name> origin/<branch-name>。

这就是多人协作的工作模式，一旦熟悉了，就非常简单。

```

小结

查看远程库信息，使用git remote -v；

本地新建的分支如果不推送到远程，对其他人就是不可见的；

从本地推送分支，使用git push origin branch-name，如果推送失败，先用git pull抓取远程的新提交；

在本地创建和远程分支对应的分支，使用git checkout -b branch-name origin/branch-name，本地和远程分支的名称最好一致；

建立本地分支和远程分支的关联，使用git branch --set-upstream branch-name origin/branch-name；

从远程抓取分支，使用git pull，如果有冲突，要先处理冲突。


### 3.2 报错2 fatal: refusing to merge unrelated histories

Git的报错

在使用Git的过程中有时会出现一些问题，那么在解决了每个问题的时候，都需要去总结记录下来，下次不再犯。

一、fatal: refusing to merge unrelated histories

今天在使用Git创建项目的时候，在两个分支合并的时候，出现了下面的这个错误。
```
~/SpringSpace/newframe on  master ⌚ 11:35:56
$ git merge origin/druid
fatal: refusing to merge unrelated histories
```
这里的问题的关键在于：fatal: refusing to merge unrelated histories

你可能会在git pull或者git push中都有可能会遇到，这是因为两个分支没有取得关系。那么怎么解决呢？

二、解决方案

在你操作命令后面加--allow-unrelated-histories

例如：
```
git merge master --allow-unrelated-histories

~/SpringSpace/newframe on  druid ⌚ 11:36:49
$ git merge master --allow-unrelated-histories
Auto-merging .gitignore
CONFLICT (add/add): Merge conflict in .gitignore
Automatic merge failed; fix conflicts and then commit the result.
```
如果你是git pull或者git push报fatal: refusing to merge unrelated histories

同理：
```
git pull origin master --allow-unrelated-histories
```
等等，就是这样完美的解决咯！

## 4 用此框架实现个人博客

，，，

