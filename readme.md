# VeryNginx
VeryNginx is a very powerful and friendly nginx

##介绍

VeryNginx基于lua_nginx_module(openrestry)开发，实现了高级的防火墙，访问统计，和其他的一些功能，大幅度强化了Nginx本身的功能，并提供了更友好的web交互界面。

###Nginx运行状态分析

* 每秒请求数
* 响应时间
* 网络流量
* 网络连接数

![Nginx运行状态](http://ww4.sinaimg.cn/mw690/3fcd0ed3jw1f0mhozu43wj20uj0mcgob.jpg)


###请求过滤
VeryNginx可以基于按照以下信息对请求进行过滤：

* IP
* UserAgent
* 请求路径(URI)
* 请求参数

![Nginx运行状态](http://ww2.sinaimg.cn/mw690/3fcd0ed3jw1f0mhp07rgoj20vb0n4gof.jpg)


VeryNginx预置了常用的过滤规则，可以一定程序上阻止常见的SQL注入，Git，SVN文件泄露，目录遍历攻击，并拦截常见的扫描工具。

同时VeryNginx的过滤器还可以支持IP黑/白名单设置

###访问统计

VeryNginx可以统计网站每个URI的访问情况，包括每个URI的:

* 总请求次数
* 各状态码次数
* 返回总字节数
* 每请求平均字节数
* 总响应时间
* 平均响应时间

并且可以按各种规则排序进行分析。

![Nginx运行状态](http://ww4.sinaimg.cn/mw690/3fcd0ed3jw1f0mhp0lq5ij20vb0n4aes.jpg)

##Installation

###1. VeryNginx is based on openresty, so you need install openresty first.

<pre>
wget https://openresty.org/download/ngx_openresty-1.9.7.1.tar.gz   
tar -xvzf ngx_openresty-1.9.7.1.tar.gz
cd ngx_openresty-1.9.7.1
sudo su
./configure --prefix=/opt/VeryNginx --user=nginx --group=nginx --with-http_stub_status_module --with-luajit
gmake
gmake install
</pre>

In fact VeryNginx used that modules in openresty:

*  lua-nginx-module
*  http_stub_status_module
*  lua cjson module

>If you don't want to install openresty , you can install that modules manually to make VeryNginx run

###2. Checkout VeryNginx repository, link nginx.conf and VeryNginx folder to nginx config dir.
<pre>
cd ~
git clone https://github.com/alexazhou/VeryNginx.git
rm -f /opt/VeryNginx/nginx/conf/nginx.conf
cp ~/VeryNginx/nginx.conf /opt/VeryNginx/nginx/conf/nginx.conf
cp -r ~/VeryNginx/VeryNginx /opt/VeryNginx/VeryNginx

#The two line behind make /opt/VeryNginx writable for nginx, so nginx can save configs in it
chown -R nginx /opt/VeryNginx 
chgrp -R nginx /opt/VeryNginx 
</pre>

###3. Configure nginx

You can add you own site config into /opt/VeryNginx/nginx/nginx.conf.

Remember don't modify the VeryNginx config file in the file.

VeryNginx config like this:
<pre>
    #-----------------VeryNginx config code------------------ 
    lua_package_path '/opt/VeryNginx/VeryNginx/lua_script/?.lua;;/opt/  VeryNginx/VeryNginx/lua_script/module/?.lua;;';
    lua_package_cpath '/opt/VeryNginx/VeryNginx/lua_script/?.so;;';   
    lua_code_cache on;

    lua_shared_dict status 1m;
    lua_shared_dict summary_long 10m;
    lua_shared_dict summary_short 10m;

    init_by_lua_file /opt/VeryNginx/VeryNginx/lua_script/on_init.lua;
    rewrite_by_lua_file /opt/VeryNginx/VeryNginx/lua_script/on_rewrite.lua;
    access_by_lua_file /opt/VeryNginx/VeryNginx/lua_script/on_access.lua;
	log_by_lua_file /opt/VeryNginx/VeryNginx/lua_script/on_log.lua;
    #---------------VeryNginx config code end-----------------

</pre>

You can also use you own Nginx config file to run VeryNginx by copying the VeryNginx configuration in /opt/VeryNginx/nginx/nginx.conf into you own configuration file. 

##Start service
 <code>/opt/VeryNginx/nginx/sbin/nginx </code>
##Stop service
 <code>/opt/VeryNginx/nginx/sbin/nginx -s stop</code>

##Configure VeryNginx
Just goto http://127.0.0.1/VeryNginx/dashboard/index.html 

And you can modify the options and goto "配置>系统>全部配置" to save it.

tips:

* After you save, new config will be used immediately. Don't need to restart or reload nginx

* When you save config, VeryNginx will write all configs to /opt/VeryNginx/VeryNginx/config.json. 

* If you did a error config so that can't login verynginx. You can delete config.json and revert VeryNginx to default config.

####Default user name and password is verynginx:verynginx

###Enjoy it~


