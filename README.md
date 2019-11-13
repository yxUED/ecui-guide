# ECUI 教程及文档

## 前言

&emsp;&emsp;ecui 是一个用于前端开发的面向事件的框架，支持前后端分离及 SPA 单页应用，支持 PC 和移动端的开发；该前端框架特别适合于交互繁多复杂的 2b 场景，对事件进行统一的管理，ecui 兼容 IE7 以上浏览器，且不需要 npm 包引入大量不稳定的插件，多数场景功能都有内置的控件实现。

## 源码地址:

这是框架核心开发者开源出来源码的地址：[https://github.com/allskystar/ECUI](https://github.com/allskystar/ECUI);

## 使用教程

初次尝试使用的，请先看后面的快速开始，深入了解，请阅读如下框架各模块的使用链接：  
[样式](doc/样式.md)  
[路由](doc/路由.md)  
[数据请求](doc/数据请求.md)  
[控件](doc/控件.md)  
[表单](doc/表单.md)  
[事件处理](doc/事件处理.md)  
[框架原理](doc/框架原理.md)

## 如何贡献代码和更新文档

希望经常使用的朋友们一起贡献代码和完善文档哈：  
文档内容放在了 GitHub 上：https://github.com/yxUED/ecui-guide，  
只有组内人员才可以对文档进行修改，由已经在组内的人员邀请的方式加入。  
文档采用[MarkDown](http://www.markdown.cn/)编写，编写人员可以 clone 下文档项目进行编辑，或者直接通过 GitHub 中每个页面的编辑按钮对文档进行编辑，编辑完毕保存修改。

## 常见问题

开发或者使用 ECUI 框架过程中遇到的问题可以添加到[问题链接](doc/问题.md)所在页。

## 快速开始

### 下载库

从 GitHub 上下载（下载前确认 Mac 上安装了 git 客户端应用。）

> git clone https://github.com/allskystar/ECUI.git

### 创建项目

按照下面示例的目录结构创建项目。

```
ECUI 框架目录）
guide(项目目录)
           |
           |_ _ _
                |_ _ _ _  helloworld
                |       |
                |       |_ _ _ _  helloworld.js
                |       |
                |       |_ _ _ _  route.helloworld.demo.html
                |       |
                |       |_ _ _ _  route.helloworld.demo.js
                |       |
                |       |_ _ _ _  route.helloworld.demo.css
                |
                |_ _ _ _  index.html
                |
                |_ _ _ _  index.js
                |
                |_ _ _ _  index.css

```

项目可以与 ECUI 框架源码放在同一个目录下。命名方式建议上面 helloworld 所在的位置相同。可以使用本 github 中的[ecui.sh](ecui.sh) 文档创建目录结构和路由，具体使用方式进入到 ecui.sh 所在目录，执行./ecui.sh -h 查看帮助文档。  
在项目中引用 ECUI 框架的方式采用在 index.html 文件中加入以下代码：

```html
<link rel="stylesheet/less" type="text/css" href="ecui.css" />
<link rel="stylesheet/less" type="text/css" href="common.css" />
<link rel="stylesheet/less" type="text/css" href="index.css" />
<script type="text/javascript" src="options.js"></script>
<script type="text/javascript" src="ecui.js"></script>
<script type="text/javascript" src="common.js"></script>
<script type="text/javascript" src="index.js"></script>
```

上面的引入文件框架必须的是 **ecui.css**、**options.js**、**ecui.js**，其他文件是为了该教材的示例的特定文件。

### 配置本地服务器

教程提供通过`Nginx`和在页面上通过`script`标签引用需要的文件来实现 ECUI 框架的配置。  
1、安装`Nginx`服务器，`Nginx`官网没有提供 Mac 安装步骤，但是`百度`或`Google`有很多的相关安装教程，这里不再赘述。  
2、配置`Nginx`配置文件。配置`Nginx`服务器,示例如下，根据实际环境（Mac brew 安装默认路径:`/usr/local/etc/nginx/nginx.conf`）

```
#控制工作进程数
#该值应根据可用内核数，磁盘，网络子系统，服务器负载等更改为最佳值
worker_processes  1;

error_log  /tmp/nginx_error.log  notice;

#包含nginx中所有处理连接的设置
events {
  #工作进程的最大连接数量
  #理论上每台nginx服务器的最大连接数为worker_processes*worker_connections
  worker_connections  1024;
}

#控制nginx http处理的所有核心特性
http {
    #该文件内定义指定文件头所对应的文件格式
    include       mime.types;
    default_type  application/octet-stream;

    log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
                      '$status $body_bytes_sent "$http_referer" '
                      '"$http_user_agent" "$http_x_forwarded_for"';

    #指定 nginx 是否调用sendfile 函数（zero copy 方式）来输出文件,对于普通应用，必须设为on。
    #如果用来进行下载等应用磁盘IO重负载应用，可设置为off，以平衡磁盘与网络IO处理速度，降低系统负载
    sendfile        on;

    #keepalive_timeout参数是一个请求完成之后还要保持连接多久，不是请求时间多久，
    #目的是保持长连接，减少创建连接过程给系统带来的性能损耗，类似于线程池，数据库连接池。
    keepalive_timeout  65;

    #upstream使nginx跨越单机的限制，完成网络数据的接收、处理和转发。
    #他不产生自己的内容，而是通过请求后端服务器得到内容
    upstream upstream_dev {
      #用于跨域请求 后端的数据，一般56为开发环境，26为测试环境
      server 192.168.155.56:80;
    }

    server {
        listen       9000;
        server_name  localhost;

        #配置车商库后台管理系统的静态资源请求规则
        location ~ ^/guide/[/\w-]+$ {
            proxy_pass http://upstream_dev;
        }
        location ~ /guide/ {
            if (!-f $request_filename) {
                rewrite '^/guide(.*)' $1;
            }
            root   /your_workspace/;
            index  index.html index.htm;
        }

        #配置默认请求为淘车拍APP 后台管理系统的静态资源
        root   /your_workspace/default; #项目所在的路径
        index  index.html index.htm;#默认请求的文件
        location = / {
            root   /your_workspace/default;
            index  login.html index.htm;
        }

        #配置项目中用到的所有ecui相关文件
        location ~ ^/(ecui\.|options\.|ie-es5\.|common\.|common/|tools/|css/|src/|images/ecui/).*$ {
          proxy_pass http://127.0.0.1:8000;
        }

        location ~ ^[/\w-]+$ {
          proxy_pass http://upstream_dev;
        }

        error_page   500 502 503 504  /50x.html;
        location = /50x.html {
            root   html;
        }
    }

    #控制开发环境中用于请求的ecui有关文件
    server {
        listen       8000;
        location / {
            #引入文件所在的路径
            root   /your_workspace/ECUI;
        }
    }
}
```

`Nginx`启动在终端运行命令，当修改配置文件后在终端运行命令

> nginx -s reload

### 模板引擎

ECUI 采用了`ETPL`模板引擎，快速了解 ETPL 模板引擎访问：[http://ecomfe.github.io/etpl/](http://ecomfe.github.io/etpl/)  
详细了解 ETPL 模板引擎的开发文档请访问：[https://github.com/ecomfe/etpl#documents](https://github.com/ecomfe/etpl#documents)

### 路由配置

教程中配置一个简单的路由，实现从**index**首页的**target**跳转到**hellowworld**，首先在`index.html`的添加**body**如下代码：

```html
<body data-ecui="load:esr" id="main">
  <!--
  <<< target:content >>>
        <div class="contentWrapper">
          <div class="left-content">
              <div class="logo"><span>ECUI</span></div>
              <div class="leftBar" id="leftBar">
                  <ul ui="type:module-link;id:list-tree">
                      <label>根</label>
                      <label class="homepage"><a href="#index">教程</a></label>
                      <ul class="quick-start-manage">
                          <label>helloworld</label>
                          <li class="quick-start-check-btn"><a href="#helloworld.demo">Demo</a></li>
                      </ul>
                  </ul>
              </div>
          </div>
          <div class="right-content">
              <div class="content-main" id="container"></div>
          </div>
      </div>
      <<< target:homepage >>>
      <div class="index-container">
        <div class="index-text">欢迎使用ECUI</div>
      </div>

      -->
</body>
```

代码中的**module-link**是 ECUI 的一个[控件]()，`index.css`的内容直接使用该项目下的`index.css`。在`index.js`中添加代码：

```js
ecui.esr.onready = function() {
  etpl.config({
    commandOpen: "<<<",
    commandClose: ">>>"
  });
  for (
    var el = document.getElementById("main").firstChild;
    el;
    el = el.nextSibling
  ) {
    if (el.nodeType === 8) {
      etpl.compile(el.textContent || el.nodeValue);
    }
  }
  etpl.config({
    commandOpen: "<!--",
    commandClose: "-->"
  });
  return {
    model: [""],
    main: "main",
    view: "content",
    onbeforerender: function(context) {},
    onafterrender: function() {}
  };
};
ecui.esr.addRoute("index", {
  main: "container",
  view: "homepage",
  onbeforerequest: function() {},
  onbeforerender: function(context) {},
  onafterrender: function() {}
});
```

上面的代码为首页添加了路由。设置路由跳转的**helloword**的**target**。  
在`route.helloworld.demo.html`中添加如下代码：

```html
<!-- target:helloworldDemo -->
<div>
  Hello world
</div>
```

如果需要的话，在`route.helloworld.demo.css`中添加需要的样式代码，在`route.helloworld.demo.js`中添加如下代码：

```js
ecui.esr.addRoute("helloworld.demo", {
  model: [""],
  main: "container",
  view: "helloworldDemo",
  onbeforerequest: function() {},
  onbeforerender: function(context) {},
  onafterrender: function(context) {}
});
```

index 路由在首页运行时候已经添加，但是**helloworld**的路由所在的**js**文件还没有加载，所以在`helloworld.js`中添加如下代码：

```js
ecui.esr.loadRoute("helloworld.demo");
```

根据配置文件在本地浏览器输入：[http://localhost:9000/guide/index.html](http://localhost:9000/guide/index.html)

### 发送请求

ECUI 的数据请求可以在路由对象的**model**中添加，一个简单的请求用户信息的示例：

```js
model: [
    'userInfo@GET base/user-info/${id}'
],
```

还可以通过`ecui.esr.request(）`的方式进行请求的发送,同样的用户信息的请求实现:

```js
ecui.esr.request('userInfo@GET base/user-info/${id}', function () {
    if (context.data instanceof Object) {
      ...
    }
});
```

上面的代码中的匿名函数是请求成功后执行的函数。其中`userInfo@GET base/user-info/${id}`是一种语法，具体句法形式为：`变量@方式 api路径?参数名=参数值`，其中方式包括`GET`、`POST`、`JOIN`、`FORM`。

### 事件管理

ECUI 的事件完全支持 DOM 事件，ECUI 事件处理函数在函数`addRoute`中添加，事件处理函数放在`onafterrender()`函数中，一个按钮的点击事件的处理函数示例代码如下：  
按钮

```html
<input ui="type:button;id:customerAddButton;" value="确认" type="button" />
```

按钮点击处理：

```js
ecui.get("customerAddButton").onclick = function() {
  // Do what you want to do.
};
```

上面的按钮也是一个 ECUI 的控件，通过`ecui.get()`方法可以获得该控件的对象，在 ECUI 中事件处理函数命名都是`on+事件`的格式。

## 如何部署

### 打包代码

打包时候需要安装 lessc,插件 和 ugligy：

> sudo npm i -g less@2.7.3  
> sudo npm i -g less-plugin-clean-css  
> sudo npm i -g uglify-es@3.3.9

安装 Java，安装过程可`百度`或者`Google`，资料很多。

ECUI 支持在开发完成好打包代码发布。首先进入到框架和项目目录的父目录，执行打包代码命令：

> ./lib-fe/build.sh 项目目录名称 \[参数\]
> 比如淘车拍：
> ./lib-fe/build.sh taochepai-h5-ecui taochepai

### 配置服务器

直接把打包后的代码上传到服务器，就像配置`vue`和`react`等开发的前端项目一样，安装配置好`Nginx`服务器即可。

## 移动端

### 创建项目

> 脚手架 npx ecui-cli init

按照下面示例的目录结构创建项目。

```
ECUI 框架目录）
guide(项目目录)
            |
            |_ _
                |_ _ _ _  _include
                |       |
                |       |_ _ _ _  controls.js
                |       |
                |       |_ _ _ _  templates.html
                |
                |_ _ _ _ helloworld
                |       |
                |       |_ _ _ _  _define_.js
                |       |
                |       |_ _ _ _  route.demo.html
                |       |
                |       |_ _ _ _  route.demo.js
                |       |
                |       |_ _ _ _  route.demo.css
                |
                |_ _ _ _  index.html
                |
                |_ _ _ _  index.js
                |
                |_ _ _ _  index.css
                |
                |_ _ _ _  _define_.js
                |
                |_ _ _ _  route.index.html
                |
                |_ _ _ _  route.index.css
                |
                |_ _ _ _  route.index.js
                |
                |_ _ _ _  .app-container.html
```

#### index.html

```html
<html>
    <head>
        <meta name="viewport" content="width=device-width, initial-scale=1, maximum-scale=1, user-scalable=no">
        <meta name="format-detection" content="telephone=no" />
        <meta charset="UTF-8">
        <title>helloworld</title>
        <link rel="stylesheet/less" type="text/css" href="ecui.css" />
        <link rel="stylesheet/less" type="text/css" href="common.css" />
        <link rel="stylesheet/less" type="text/css" href="index.css" />
        <script type="text/javascript" src="options.js"></script>
        <script type="text/javascript" src="ecui.js"></script>
        <script type="text/javascript" src="common.js"></script>
        <script type="text/javascript" src="index.js"></script>
    </head>
    <body data-ecui="load:esr(app=true)">
    <body>
</html>
```

上面是以一个项目的标准引入的 css 和 js，如果是单纯的框架引入，只需要引入 ecui.css、options.js、ecui.js。

### 模板引擎

ECUI 采用了`ETPL`模板引擎，快速了解 ETPL 模板引擎访问：[http://ecomfe.github.io/etpl/](http://ecomfe.github.io/etpl/)  
详细了解 ETPL 模板引擎的开发文档请访问：[https://github.com/ecomfe/etpl#documents](https://github.com/ecomfe/etpl#documents)

### 路由配置

在初始化 ECUI 工作环境的过程中会根据 body 的 data-ecui 元素 load:esr 来调用 esr 路由的 load 方法，该方法会调用 esr 的 onready 的钩子。在 onready 方法中配置缺省路由。  
在 index.js 中配置缺省路由，

```js
ecui.esr.onready = function() {
  return {
    model: [],
    onbeforerender: function(context) {},
    onafterrender: function(context) {}
  };
};
```

在 index.css 中加入基本样式

```css
.app-container {
  display: -webkit-flex;
  display: flex;
  -webkit-flex-direction: column;
  flex-direction: column;
  width: 100%;
  height: 100%;
  background: #f4f4f4;
  overflow-y: auto;
  overflow-x: hidden;

  .header-wrap {
    position: relative;
    width: 100%;
    height: px2rem(160px);
    -webkit-flex-grow: 0;
    flex-grow: 0;
    z-index: 1;

    > div {
      position: relative;
      font-size: px2rem(36px);
      background: #2784e2;
      color: #fff;
      width: 100%;
      height: 100%;
      display: -webkit-flex;
      display: flex;
      -webkit-flex-direction: row;
      flex-direction: row;
      align-items: center;
      .left-wrap {
        -webkit-flex: 1;
        flex: 1;
        color: #fff;
      }

      .title-wrap {
        -webkit-flex: 3;
        flex: 3;
        text-align: center;
        white-space: nowrap;
        text-overflow: ellipsis;
        overflow: hidden;

        .title-right {
          position: absolute;
          right: px2rem(24px);
          font-size: px2rem(28px);
        }
      }
      .right-wrap {
        -webkit-flex: 1;
        flex: 1;
        color: #fff;
      }
    }
  }
  .content-wrap {
    position: relative;
    width: 100%;
    -webkit-flex-grow: 1;
    flex-grow: 1;
    -webkit-flex: 1;
    flex: 1;
    overflow: hidden;

    > div {
      position: absolute;
      width: 100%;
      height: 100%;
      overflow-y: auto;
    }
  }
}
```

### 层（Layer）

在 ECUI 框架中引入了层（Layer）的概念，在移动端开发中，层的引入解决了路由切换时候的动画效果实现和首屏加载长时间白屏的问题。在移动开发中的每个层由两部分组成 header 和 container。一个空白的层如下：

```html
<header></header>
<container> </container>
```

在 header 中可以添加头部的一些元素， 比如返回按钮，title，右部的操作选项等。在 container 中可以添加与数据无关的静态的资源，在对应的层的 css 文件中添加需要的样式后，可以极大的缩短首屏白屏的时间，如果是内嵌到 app 中的 H5 页面，实现 app 本地加载静态资源的话体验会更好。
.app-container.html 是整个 app 的容器，在该文件中添加如下代码：

```html
<div class="app-container">
  <!--{include file=".layers.html"}-->
  <div style="display:none">
    <div class="left-wrap">
      <div ui="type:ui.base.Back" class="back">
        <span class="left-text"><</span>
      </div>
    </div>
    <div class="title-wrap">
      <strong ui="ext-data:AppCommonTitle@()">=$</strong>
    </div>
    <div class="right-wrap"></div>
  </div>
  <div
    ui="type:ecui.esr.AppLayer"
    id="AppCommonContainer"
    style="display:none"
  ></div>
  <div style="display:none">
    <div class="left-wrap">
      <div ui="type:ui.base.Back" class="back">
        <span class="left-text"><</span>
      </div>
    </div>
    <div class="title-wrap"></div>
    <div class="right-wrap"></div>
  </div>
  <div
    ui="type:ecui.esr.AppLayer"
    id="AppSelectContainer"
    style="display:none"
  ></div>
  <div ui="ext-ios-fixed:top" class="header-wrap"></div>
</div>
<div id="dialogContainer"></div>
```

app-container 容器中添加了默认的两个 layer 层，AppCommonContainer 是一个公共的路由容器，如果不需要首屏加载静态资源显示，并且可以使用公共的 header，可以把对应的路由放入到该容器当中。AppSelectContainer 是一个用来作为 select 弹出的层容器。把需要弹出的内容放入其中。
.layers.html 文件是一个层元素的集合，通过 include 将.layers.html 文件中的所有层元素引入到 app-container 中。  
在路由切换的时候，可以通过设置路由的 weight 属性来决定层的动画方向，目标路由的权重值大于原路由的权重值时候左滑，小于的时候右滑，等于的时候没有动画。

### index 路由

index 路由作为首屏加载的路由，为该路单独设置 layer。
layer.index.html

```html
<header>
  <div class="left-wrap"></div>
  <div class="title-wrap">
    index
  </div>
  <div class="right-wrap"></div>
</header>
<container>
  <div>index static</div>
  <div id="index_dynamic"></div>
</container>
```

route.index.html

```html
<!-- target: index -->
<div>
  index dynamic
</div>
```

route.index.js

```js
ecui.esr.addRoute("index", {
  model: ["data@GET xxxxxx"],
  frame: true,
  view: "index",
  weight: 1,
  main: "index_dynamic",
  oncached: function(context) {},
  onbeforerequest: function(context) {},
  onbeforerender: function(context) {},
  onafterrender: function(context) {}
});
```

上面的事例中 ecui.esr.addRoute 方法的第一个参数是路由的名称，第二个参数是路由对象。其中 model 是加载该路由需要通过请求而的到的数据。frame 是可以使页面在数据请求之前就渲染出来。view 是路由对应的 target 模板。main 是该 view 模板最终要放置的位置。如果没有 layer 中的静态资源，默认 main 的值为路由对应 container 的 id。路由默认第一加载路由的时候是不带缓存的，之后切换路由会带缓存，如果希望路由每次加载都请求数据，设置路由的 CACHE 为 false，如果是从特定路由切换过来需要重新加载路由，则在 url 后面添加~DENY*CACHE。如果路由没有缓存，切换路由则会执行 onbeforerequest，onbeforerender，onafterrender 等生命周期函数。如果路由已经被缓存，则切换路由时候会执行 oncached 生命周期函数。
\_define*.js

```js
ecui.esr.loadRoute("index");
```

在最外层的*define*.js 中加载 index 路由。

### helloword 路由

index 之外的模块需要创建模块命名的目录，并且在该目录下创建*define*.js 来加载模块中的路由，及其路由对应的 html、js、css 文件。  
route.demo.html

```html
<!-- target:helloworld -->
<div>
  Hello world
</div>
```

route.demo.js

```js
ecui.esr.addRoute("demo", {
  model: [""],
  main: "AppCommonContainer",
  view: "helloworld",
  weight: 2,
  onbeforerequest: function() {},
  onbeforerender: function(context) {
    ecui.esr.setData("AppCommonTitle", "Demo");
  },
  onafterrender: function(context) {}
});
```

_define_.js

```js
ecui.esr.loadRoute("demo");
```

### 公共资源

如果目录名称是”\_“开头的，则在打包的时候不会将该目录作为模块打包，可以在该目录中放置公共的组件和模板。如上面用到的 ui.base.Back 控件，就可以作为一个公共的组件在项目中使用。
在\_include 目录的 controls.js 中添加如下代码：

```js
ui.base.Back = ecui.inherits(
  ecui.ui.Control,
  function(el, options) {
    ecui.ui.Control.call(this, el, options);
  },
  {
    onclick: function() {
      window.history.go(-1);
    }
  }
);
```

在 index.js 中添加如下代码：

```js
ui.base = {};
document.write(
  '<script type="text/javascript" src="_include/controls.js"></script>'
);
```

### 配置本地服务器

教程提供通过`Nginx`和在页面上通过`script`标签引用需要的文件来实现 ECUI 框架的配置。  
1、安装`Nginx`服务器，`Nginx`官网没有提供 Mac 安装步骤，但是`百度`或`Google`有很多的相关安装教程，这里不再赘述。  
2、配置`Nginx`配置文件。配置`Nginx`服务器,示例如下，根据实际环境（Mac brew 安装默认路径:`/usr/local/etc/nginx/nginx.conf`）

```
#控制工作进程数
#该值应根据可用内核数，磁盘，网络子系统，服务器负载等更改为最佳值
worker_processes  1;

error_log  /tmp/nginx_error.log  notice;

#包含nginx中所有处理连接的设置
events {
  #工作进程的最大连接数量
  #理论上每台nginx服务器的最大连接数为worker_processes*worker_connections
  worker_connections  1024;
}

http {
    include       mime.types;
    default_type  application/octet-stream;

    log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
                      '$status $body_bytes_sent "$http_referer" '
                      '"$http_user_agent" "$http_x_forwarded_for"';

    #access_log  logs/access.log  main;

    sendfile        on;
    #tcp_nopush     on;

    #keepalive_timeout  0;
    keepalive_timeout  10;

    #gzip  on;

    # another virtual host using mix of IP-, name-, and port-based configuration

    upstream upstream_dev {
     	server dev.oa.bitauto.com;
    }
    server {
        listen       80;
        #add_header   Cache-Control    no-cache;
        add_header Cache-Control 'no-store,no-cache,must-revalidate';
#        add_header xx-via  'developer-wd';
        #server_name  somename  alias  another.alias;

        location = /common/css/UICONST.css {
            root  /Users/yiche/work/ECUI;
        }
        root   /Users/yiche/work;
        location ~ ^/.*/(ie-es5\.|ecui\.|options\.|common\.|update\.|common/|tools/|css/|src/|images/ecui/).*$ {
            if ( !-f $document_root$uri ) {
                rewrite /[^/]+/(.*) /ECUI/$1;
            }
            root   /Users/yiche/work;
            index  index.html index.htm;
        }

        location ~ ^/output-.*/[/\w-]+$ {
            rewrite /output-(.*) /$1;
        }

        location ~ ^[/\w-]+$ {
    	    #proxy_set_header Host 'test.oa.bitauto.com';
    	    proxy_set_header Host 'dev.oa.bitauto.com';
            proxy_pass http://upstream_dev;
        }
        location ~ /(_define_\.css|layer\.[^/]+\.(js|css|html))$ {
            if ( !-f $document_root$uri ) {
                # 打包后自然会恢复
                # default_type text/html;
                add_header Content-Type 'text/html; charset=utf-8';
                return 200 '';
            }
            root   /Users/yiche/work;
        }

        location / {
            root   /Users/yiche/work;
            index  index.html index.htm;
        }
    }
}
```

`Nginx`启动在终端运行命令，当修改配置文件后在终端运行命令

> nginx -s reload
> 根据配置文件在本地浏览器输入：[http://localhost/guide/index.html](http://localhost/guide/index.html)

## 部署

### 打包代码

打包时候需要安装 lessc,插件 和 ugligy：

> sudo npm i -g less@2.7.3  
> sudo npm i -g less-plugin-clean-css
> sudo npm i -g uglify-es@3.3.9 (目前不用装该包)

安装 Java，安装过程可`百度`或者`Google`，资料很多。

ECUI 支持在开发完成好打包代码发布。首先进入到框架和项目目录的父目录，执行打包代码命令：

> ./ECUI/build.sh 项目目录名称 \[参数\]
