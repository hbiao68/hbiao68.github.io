@[toc]

# 一、文章参考
1. [如何查看chrome浏览器插件位置 ](http://www.xitongcheng.com/jiaocheng/dnrj_article_68058.html)


# 二、问题描述
最近在学习Vue3的语法知识，其中需要安装chrome开发调试插件，由于网络的原因，无法访问chrome商店，也无法从github中下载源码（否则就可以本地编译了）

# 三、解决办法

1. 让国外的朋友从chrome应用商店下载安装到他本地，此时他电脑会将下载的插件安装程序删除，无法发给我

2. 通过chrome查找到安装组件的id值，并记录下来

![请添加图片描述](https://img-blog.csdnimg.cn/0cd89d4c0eae4c3bbaeda52ea00eb41a.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBA6IOW6bmFNjg=,size_16,color_FFFFFF,t_70,g_se,x_16)

3. 如果对方电脑是windows，可以访问“**C:\Users\administrator\AppData\Local\Google\Chrome\User Data\Default\Extensions**”查找到对应id值的文件夹，然后压缩成zip包

![请添加图片描述](https://img-blog.csdnimg.cn/a29209d8b82f49628a2ba90708610b3a.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBA6IOW6bmFNjg=,size_20,color_FFFFFF,t_70,g_se,x_16)

4. 打开chrome插件管理器，打开开发者模式

![请添加图片描述](https://img-blog.csdnimg.cn/8e4736de6e0246af94dc3429caba201b.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBA6IOW6bmFNjg=,size_20,color_FFFFFF,t_70,g_se,x_16)

5. 将前面的zip包解压，然后文件夹拖拽到插件面板中
![请添加图片描述](https://img-blog.csdnimg.cn/c13a14fcc7c0474db91476abdf91fdac.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBA6IOW6bmFNjg=,size_20,color_FFFFFF,t_70,g_se,x_16)
![请添加图片描述](https://img-blog.csdnimg.cn/18b385ced65647c98c332c9a653f7671.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBA6IOW6bmFNjg=,size_20,color_FFFFFF,t_70,g_se,x_16)




