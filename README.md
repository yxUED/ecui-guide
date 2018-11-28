# ECUI 教程及文档

## 前言
&emsp;&emsp;ecui是一个用于前端开发的面向事件的框架，支持前后端分离及SPA单页应用，支持PC和移动端的开发；该前端框架特别适合于交互繁多复杂的2b场景，对事件进行统一的管理，ecui兼容IE7以上浏览器，且不需要npm包引入大量不稳定的插件，多数场景功能都有内置的控件实现。

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
文档内容放在了GitHub上：https://github.com/yxUED/ecui-guide，   
只有组内人员才可以对文档进行修改，由已经在组内的人员邀请的方式加入。     
文档采用[MarkDown](http://www.markdown.cn/)编写，编写人员可以clone下文档项目进行编辑，或者直接通过GitHub中每个页面的编辑按钮对文档进行编辑，编辑完毕保存修改。

## 常见问题
开发或者使用ECUI框架过程中遇到的问题可以添加到[问题链接](doc/问题.md)所在页。

## 快速开始

### 下载库
从GitHub上下载（下载前确认Mac上安装了git客户端应用。）  
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
项目可以与ECUI框架源码放在同一个目录下。命名方式建议上面helloworld所在的位置相同。可以使用本github中的[ecui.sh](ecui.sh) 文档创建目录结构和路由，具体使用方式进入到ecui.sh所在目录，执行./ecui.sh -h查看帮助文档。         
在项目中引用ECUI框架的方式采用在index.html文件中加入以下代码：

```html
<link rel="stylesheet/less" type="text/css" href="ecui.css" />
<link rel="stylesheet/less" type="text/css" href="common.css">
<link rel="stylesheet/less" type="text/css" href="index.css">
<script type="text/javascript" src="options.js"></script>
<script type="text/javascript" src="ecui.js"></script>
<script type="text/javascript" src="common.js"></script>
<script type="text/javascript" src="index.js"></script>
```

上面的引入文件框架必须的是 **ecui.css**、**options.js**、**ecui.js**，其他文件是为了该教材的示例的特定文件。
### 配置本地服务器
教程提供通过`Nginx`和在页面上通过`script`标签引用需要的文件来实现ECUI框架的配置。     
1、安装`Nginx`服务器，`Nginx`官网没有提供Mac安装步骤，但是`百度`或`Google`有很多的相关安装教程，这里不再赘述。     
2、配置`Nginx`配置文件。配置`Nginx`服务器,示例如下，根据实际环境（Mac brew安装默认路径:`/usr/local/etc/nginx/nginx.conf`）

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
>nginx -s reload

### 模板引擎
ECUI采用了`ETPL`模板引擎，快速了解ETPL模板引擎访问：[http://ecomfe.github.io/etpl/](http://ecomfe.github.io/etpl/)  
详细了解ETPL模板引擎的开发文档请访问：[https://github.com/ecomfe/etpl#documents](https://github.com/ecomfe/etpl#documents)

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
代码中的**module-link**是ECUI的一个[控件]()，`index.css`的内容直接使用该项目下的`index.css`。在`index.js`中添加代码：

```js
ecui.esr.onready = function () {
    etpl.config({
        commandOpen: '<<<',
        commandClose: '>>>'
    });
    for (var el = document.getElementById('main').firstChild; el; el = el.nextSibling) {
        if (el.nodeType === 8) {
            etpl.compile(el.textContent || el.nodeValue);
        }
    }
    etpl.config({
        commandOpen: '<!--',
        commandClose: '-->'
    });
    return {
        model: [''],
        main: 'main',
        view: 'content',
        onbeforerender: function (context) {
        },
        onafterrender: function () {
        }
    };
};
ecui.esr.addRoute('index', {
    main: 'container',
    view: 'homepage',
    onbeforerequest: function () {
    },
    onbeforerender: function (context) {
    },
    onafterrender: function () {
    }
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
ecui.esr.addRoute('helloworld.demo', {
    model: [''],
    main: 'container',
    view: 'helloworldDemo',
    onbeforerequest: function () {
    },
    onbeforerender: function (context) {
    },
    onafterrender: function (context) {
    },
  });
```

index路由在首页运行时候已经添加，但是**helloworld**的路由所在的**js**文件还没有加载，所以在`helloworld.js`中添加如下代码：

```js
ecui.esr.loadRoute('helloworld.demo');
```

根据配置文件在本地浏览器输入：[http://localhost:9000/guide/index.html](http://localhost:9000/guide/index.html)
### 发送请求
ECUI的数据请求可以在路由对象的**model**中添加，一个简单的请求用户信息的示例：

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
ECUI的事件完全支持DOM事件，ECUI事件处理函数在函数`addRoute`中添加，事件处理函数放在`onafterrender()`函数中，一个按钮的点击事件的处理函数示例代码如下：   
按钮

```html
<input ui="type:button;id:customerAddButton;" value="确认" type="button"/>
```

按钮点击处理：

```js
ecui.get('customerAddButton').onclick = function () {
    // Do what you want to do.
};
```

上面的按钮也是一个ECUI的控件，通过`ecui.get()`方法可以获得该控件的对象，在ECUI中事件处理函数命名都是`on+事件`的格式。

## 如何部署
### 打包代码

打包时候需要安装lessc,插件 和ugligy：  
>sudo npm i -g less@2.7.3   
>sudo npm i -g less-plugin-clean-css   
>sudo npm i -g uglify-es@3.3.9  

安装Java，安装过程可`百度`或者`Google`，资料很多。

ECUI支持在开发完成好打包代码发布。首先进入到框架和项目目录的父目录，执行打包代码命令：   
>./lib-fe/build.sh 项目目录名称  \[参数\]
比如淘车拍：
>./lib-fe/build.sh taochepai-h5-ecui  taochepai


### 配置服务器
直接把打包后的代码上传到服务器，就像配置`vue`和`react`等开发的前端项目一样，安装配置好`Nginx`服务器即可。
