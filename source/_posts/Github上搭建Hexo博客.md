---
title: Github Pages上搭建Hexo博客
date: 2016-03-16 11:22:55
tags:
---

安装Git和NodeJs
------
安装hexo
------
* 去github上创建仓库，name.github.io,示例：ckj375.github.io
* 创建2个分支：master和hexo，并设置hexo为默认分支，hexo分支用于存放网站原始文件，master分支用于存放生成的静态网页。
* 克隆代码仓  
```
git clone https://github.com/ckj375/ckj375.github.io.git
```
* 进入目录，依次执行以下命令
```
hexo init  
npm install  
npm install hexo-deployer-git --save  
hexo generate  
hexo server  
```
* 打开本地浏览器按照提示输入url即可访问本地博客了,若提示找不到hexo server这条指令,输入
```
npm install hexo -server --save
```
* 修改_config.yml中的deploy参数（注意冒号后面要加空格）
```
deploy:  
    type: git  
    repo: https://github.com/ckj375/ckj375.github.io.git  
    branch: master
```
* 网站作者和标题：修改站点配置文件_config.yml的author和title字段
```
author: chenkaijian
title: 皮皮熊的博客
```
* 通过Git将网站原始文件提交至hexo分支：
```
git push origin hexo
hexo generate 生成网站
hexo deploy 将网站部署到github的master（前面已经在站点配置文件_config.yml中配置deploy参数）
```
浏览器打开http://ckj375.github.io/即可查看效果了

hexo常用命令
------
```
hexo help #查看帮助  
hexo init #初始化一个目录  
hexo new "postName" #新建文章  
hexo new page "pageName" #新建页面  
hexo generate #生成网页，可以在 public 目录查看整个网站的文件  
hexo server #本地预览  
hexo deploy #部署.deploy目录  
hexo clean #清除缓存，建议每次执行命令前先清理缓存，每次部署前先删除 .deploy 文件夹
```
简写  
```
hexo n == hexo new  
hexo g == hexo generate  
hexo s == hexo server  
hexo d == hexo deploy  
```

主题配置
------
个人比较喜欢简约风的主题，所以选了Next主题  
进入仓库根目录克隆最新版本的next主题：
```
git clone https://github.com/iissnan/hexo-theme-next themes/next
```
修改仓库根目录下的_config.yml配置项theme
theme: next

**选择Scheme**  
Scheme是Next提供的一种特性，借助于Scheme，NexT为你提供多种不同的外观。目前Next支持三种Scheme:
- Muse - 默认Scheme，这是Next最初的版本，黑白主调，大量留白
- Mist - Muse的紧凑版本，整洁有序的单栏外观
- Pisces - 双栏Scheme，小家碧玉似的清新  
修改主题目录下的_config.yml配置文件  
scheme: Mist

**设置语言**  
编辑站点配置文件，将language设置成你所需要的语言。建议明确设置你所需要的语言，例如选用简体中文，配置如下：language: zh-Hans

**设置菜单**  
菜单配置包括两个部分，第一是菜单项（名称和链接），第二是菜单项的显示文本，第三是菜单项对应的图标。
打开主题配置文件根据需要自行配置：
```
Menu Settings
menu:
  home: /
  #categories: /categories
  #about: /about
  archives: /archives
  tags: /tags
  #commonweal: /404.html
```


**设置头像**  
放置在 source/images/ 目录下  
配置为：avatar: /images/avatar.png

集成第三方服务
------
**多说评论**  
首页选择我要安装，创建站点信息。多说域名这一栏即是你的多说域名，示例：
```
http://ckj375.duoshuo.com
```
在站点配置文件中新增duoshuo_shortname字段   
duoshuo_shortname: ckj375

**Cnzz统计**  
注册账号创建站点，进入统计代码页面，有多种统计样式可供选择。
示例：在themes/next/layout/_partials/目录下新建cnzz_tongji.swig，添加以下代码
```
<% if (theme.cnzz){ %>
<script type="text/javascript">var cnzz_protocol = (("https:" == document.location.protocol) ? " https://" : " http://");document.write(unescape("%3Cspan id='cnzz_stat_icon_1257888989'%3E%3C/span%3E%3Cscript src='" + cnzz_protocol + "s4.cnzz.com/z_stat.php%3Fid%3D1257888989%26online%3D2' type='text/javascript'%3E%3C/script%3E"));</script>
<% } %>
```
js部分可换成自己想要的样式,
在footer.swig中新增以下代码
```
<div class="analytics">
<script type="text/javascript">var cnzz_protocol = (("https:" == document.location.protocol) ? " https://" : " http://");document.write(unescape("%3Cspan id='cnzz_stat_icon_1257888989'%3E%3C/span%3E%3Cscript src='" + cnzz_protocol + "s4.cnzz.com/z_stat.php%3Fid%3D1257888989%26online%3D2' type='text/javascript'%3E%3C/script%3E"));</script>
</div>
```
同样js部分可自行替换  
在主题配置文件中启用cnzz统计  
cnzz_tongji: true

编辑文章
------
新建文章
```
hexo new "标题"
```
在 _posts 目录下会生成文件标题.md

```
---
title: Github Pages上搭建Hexo博客
date: 2016-03-16 11:22:55
tags:
---
```
接下来就用markdown尽情书写吧！

[阅读原文](http://ckj375.github.io/2016/03/16/Github%E4%B8%8A%E6%90%AD%E5%BB%BAHexo%E5%8D%9A%E5%AE%A2/)
