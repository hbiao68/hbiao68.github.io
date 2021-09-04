​

# 一、centOS7 环境 安装

## 1.1 安装步骤
1. 添加 nginx 到 yum 源中
> sudo rpm -Uvh http://nginx.org/packages/centos/7/noarch/RPMS/nginx-release-centos-7-0.el7.ngx.noarch.rpm

2. 安装 nginx （在吧nginx添加到 yum 源之后，就可以使用 yum 安装了）
> sudo yum install -y nginx

3. 启动 nginx
> sudo systemctl start nginx.service

4. 验证
在浏览器中输入服务器的ip地址： `http://目标IP`

## 1.2 nginx 安装目录说明

1. 查看安装目录命令
> whereis nginx

2. 执行目录：/usr/sbin/nginx

3. 模块所在目录：/usr/lib64/nginx/modules

4. 配置所在目录：/etc/nginx/

5. 默认站点目录：/usr/share/nginx/html

## 1.3 启停服务器命令

1. 启动服务 `systemctl start nginx.service`

2. 关闭服务 `systemctl stop nginx.service`

3. 重启服务 `systemctl reload nginx.service`

4. 查看服务状态 `systemctl status nginx.service`


# 二、案例：将nginx当做文件服务器

## 2.1 需求说明

现在文件已经归档到NAS存储上，但是相对于服务器而言，只是挂载到本地目录，因此，需要使用nginx方式提供可视化的下载

## 2.2 Nginx设置目录浏览并配置验证

1. 在nginx/conf/nginx.conf中添加配置

```js
location /huangbiao/ {
  auth_basic “hb123456”;#中间不能有空格
  auth_basic_user_file /tmp/htpasswd;#指名密码账号文件，不能为.htpasswd
  alias /home/; #指名浏览目录
  autoindex on; #开启nginx目录浏览功能
  autoindex_exact_size off; #文件大小从KB开始显示
  autoindex_localtime on; #显示文件修改时间为服务器本地时间
}
```
 

2. 添加账号密码文件 /tmp/htpasswd

[root@eccs_web tmp]# ls
hsperfdata_root  htpasswd

备注：htpasswd 文件使用 Apache的htpasswd命令创建，请参考[启用apache目录浏览功能 二（账号验证、IP网段限制） ](http://hbiao68.iteye.com/blog/2165087)


​