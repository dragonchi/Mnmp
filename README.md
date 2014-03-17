MNMP
====

mac搭建NMP环境

安装:
把: git@github.com:dragonchi/MNMP.git的脚本下载下来.
安装前请确认安装了homebrew, 就不提了. 开始安装:
bash切换到setup-mnmp.sh目录, 然后执行:sh setup-mnmp.sh 就等着自动安装吧.



必要的一些配置:
nginx:
vim /usr/local/etc/nginx/nginx.conf
http {} 里面最后面加上: include vhost/*.conf;/
vim /usr/local/etc/nginx/vhost/default.conf , 添加类似下面的内容:

server { 
    listen       8080; 
    server_name  dev.local; 
    root   /Users/leon/Development; 
    index  index.html index.htm index.php; 
 
    autoindex on; 
    autoindex_exact_size off; 
    autoindex_localtime on; 
    #error_page  404 /404.html; 
    error_page   500 502 503 504  /50x.html; 
    location = /50x.html {root html;} 
    location ~ .*\.(php|php5)?$ { 
        fastcgi_pass 127.0.0.1:9000; 
        fastcgi_index index.php; 
        include fastcgi.conf; 
    } 
    location / { 
        if (!-e $request_filename){ 
            rewrite ^/sitemap.xml$ /sitemap.php; # rewrite 
        } 
    } 
    access_log off; 
} 
sudo sh -c "echo '127.0.0.1 dev.local' >> /etc/hosts"
上面的nginx配置和命令是绑定http://dev.local作为域名, 绑定到/Users/leon/Development目录(域名和目录根据自己的需要修改吧).


php-fpm:
vim /usr/local/etc/php/5.4/php-fpm.conf, 找到并修改下面四行, 后面两个是要注释掉的:
error_log = /tmp/php-fpm.log
daemonize = yes
;user = _www
;group = _www
另外php.ini的路径: /usr/local/etc/php/5.4/php.ini, 如果有额外需求自己修改.

mysql:
基本不需要配置了, 配置文件在/usr/local/opt/mysql/my-new.cnf 如果没有就是 my.cnf.
默认不需密码, 如果需要可以执行: mysql_secure_installation 一步步来, 本地开发意义不大.

启动重启service脚本:
设置权限: chmod +x ./mnmp.sh
然后运行: ./mnmp.sh start | stop | restart 即可.
推荐加到profile里面, 比如: echo "alias mnmp='/Users/你的路径/mnmp.sh'" >> ~/.bash_profile
source ~/.bash_profile



