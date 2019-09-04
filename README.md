## 前言
比较喜欢看小说，一直想模仿起点做一个小说神器。所以用vue做一个移动端的神器，这是第一个版本，api使用追书神器提供的api，但是api有时候不稳定，通常会失效。所以以后会用爬虫去抓一些数据存到数据库，重新做一个本地版本的小说阅读器。
## 技术
vue2 + vuex + vue-router + webpack4 + ES6 + axios + scss
## github地址
https://github.com/yaowuya/vue-qidian-web
## 部署地址
已经过期
## 项目运行
```
git clone https://github.com/yaowuya/vue-qidian-web.git

npm install

npm run dev //本地运行 访问：http://localhost:9528

npm run build //部署上线 生成的dist文件夹放到服务器中即可：需要配置代理，如使用nginx，可参考下面问题中的配置
```


## 开发中记录的笔记
>1. vw：1vw等于视口宽度的1%。
>2. vh：1vh等于视口高度的1%。
>3. vmin：选取vw和vh中最小的那个。
>4. vmax：选取vw和vh中最大的那个。

>vue-router 默认 hash 模式 —— 使用 URL 的 hash 来模拟一个完整的 URL，于是当 URL 改变时，页面不会重新加载。
 如果不想要很丑的 hash，我们可以用路由的 history 模式，这种模式充分利用 history.pushState API 来完成 URL 跳转而无须重新加载页面。
 当你使用 history 模式时，URL 就像正常的 url，例如 http://yoursite.com/user/id，也好看！
 不过这种模式要玩好，还需要后台配置支持。因为我们的应用是个单页客户端应用，如果后台没有正确的配置，当用户在浏览器直接访问 http://oursite.com/user/id 就会返回 404，这就不好看了。
 所以一般情况下，将这个模式去掉
 
>移动端引入 lib-flexible时注意事项（important）: 由于flexible会动态给页面header中添加`<meta name='viewport' >`标签，所以务必请把目录 public/index.html 中的这个标签删除！！！[追书神器api](https://github.com/amumu233/zhuishushenqi/wiki/API-%E6%8E%A5%E5%8F%A3%E6%96%87%E6%A1%A3)

## nginx 配置
```nginx
# For more information on configuration, see:
#   * Official English Documentation: http://nginx.org/en/docs/
#   * Official Russian Documentation: http://nginx.org/ru/docs/

user nginx;
worker_processes auto;
error_log /var/log/nginx/error.log;
pid /run/nginx.pid;

# Load dynamic modules. See /usr/share/nginx/README.dynamic.
include /usr/share/nginx/modules/*.conf;

events {
    worker_connections 1024;
}

http {
    log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
                      '$status $body_bytes_sent "$http_referer" '
                      '"$http_user_agent" "$http_x_forwarded_for"';

    access_log  /var/log/nginx/access.log  main;

    sendfile            on;
    tcp_nopush          on;
    tcp_nodelay         on;
    keepalive_timeout   65;
    types_hash_max_size 2048;

    include             /etc/nginx/mime.types;
    default_type        application/octet-stream;

    # Load modular configuration files from the /etc/nginx/conf.d directory.
    # See http://nginx.org/en/docs/ngx_core_module.html#include
    # for more information.
    include /etc/nginx/conf.d/*.conf;

    server {
        listen       80;
        server_name  39.108.227.35;
        #root         /usr/share/nginx/html;

        # Load configuration files for the default server block.
        include /etc/nginx/default.d/*.conf;
		
		proxy_redirect off;
		# 把host头传过去，后端服务程序将收到your.domain.name, 否则收到的是localhost:8080
		proxy_set_header Host $host;
		proxy_set_header  X-Real-IP  $remote_addr;
		proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for; 
		
        location  / {
			root  /root/qidian/web/dist/;		
			index index.html index.htm;	
			#官网介绍设置这条可以解决history路由的问题
			try_files $uri $uri/ @router;
        }
		
		location  /chapterapi/ {
			proxy_pass http://chapter2.zhuishushenqi.com/chapter/http://vip.zhuishushenqi.com/;
			
        }
		
		location /api/ {
			proxy_pass http://api.zhuishushenqi.com/;
        }
        
		
        error_page 404 /404.html;
            location = /40x.html {
        }

        error_page 500 502 503 504 /50x.html;
            location = /50x.html {
        }
    }
}

```
## 部分截图
#### 书架
![我的书架](https://github.com/yaowuya/vue-qidian-web/blob/master/gitimg/shelf.png)
#### 书籍详情
![书籍详情](https://github.com/yaowuya/vue-qidian-web/blob/master/gitimg/book.png)
#### 阅读
![阅读](https://github.com/yaowuya/vue-qidian-web/blob/master/gitimg/read.png)
#### 首页
![首页](https://github.com/yaowuya/vue-qidian-web/blob/master/gitimg/home.png)
#### 排行
![排行](https://github.com/yaowuya/vue-qidian-web/blob/master/gitimg/rank.png)
#### 搜索
![搜索](https://github.com/yaowuya/vue-qidian-web/blob/master/gitimg/search.png)
#### 分类
![分类](https://github.com/yaowuya/vue-qidian-web/blob/master/gitimg/category.png)
