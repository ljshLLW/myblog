---
title: Tomcat免安装版踩坑指南
copyright: ture
date: 2019-05-12 10:58:01
categories: Web
tags:
- Tomcat
images: "images/img/tomcat.jpg"
---
## 下载解压
从官网下载Tomcat的压缩包解压到硬盘上(这里用的是toncat7)，解压之后目录如下(Windows)
- <code>bin</code> 存放tomcat的一些命令脚本
- <code>conf</code> 存放配置文件
- <code>lib </code> 存放运行时库文件
- <code>logs</code> 存放日志
- <code>temp</code> 存放运行过程中产生的临时文件
- <code>webapps</code> 存放应用文件（需要部署的工程放这）
- <code>work</code>  存放运行时产生的class文件
- <code>LICENSE</code> 许可证
- <code>NOTICE</code> 注意事项
- <code>RELEASE_NOTES</code>  版本说明
- <code>RUNNING.txt</code> 运行相关解释 <code></code>    

<!-- more -->
## startup.bat  

我们需要运行tomcat的时候只需要找到<code>/bin/startup.bat</code>点击运行即可运行结果类似下图。  
![startup](startup正常运行.png)
不要关闭这个命令窗口，打开浏览器访问<code>http://localhost:8080/</code>或者<code>http://127.0.0.1:8080/</code>即可访问到如下页面  
![8080](8080.png) 
如果想要结束的话点击<code>/bin/shutdown.bat</code>即可
### 闪退
点击<code>startup.bat</code>的时候，出现命令窗口闪一下又没了，此时需要用命令行窗口进入到<code>Tomcat/bin/</code>目录下键入
```
startup.bat 
```
#### 弹出另一个窗口输出一系列代码然后消失
其实弹出窗口的代码已经说明了错误所在，但是因为太快我们没看清，这时候log目录就派上用场了，进入log寻找<code>catalina.xxxx-xx-xx.log</code>,通常这类文件都有很多可以点击修改日期栏使文件按照日期顺寻排序，方便寻找。打开之后里面记录了问题的原因，多半是因为端口占用，所以启动不了，杀掉占用的进程就ok了，还有可能是你之前启动了忘了<code>shutdown.bat</code>关闭Tomcat所以只需要点一下<code>shutdown.bat</code>再点<code>startup.bat</code>就可以了（这也是进程占用端口）
#### java_home 
提示错误如下
```
Neither the JAVA_HOME nor the JRE_HOME environment variable is defined At least one of these environment variable is needed to run this program;
```
很显然意思就是没有设置JAVA_HOME这个环境变量，Tomcat运行时需要jre的支持，我们安装的jkd中默认包含了jre，所以只需要设置JAVA_HOME为jdk安装目录即可例如<code>C:\Program Files\Java\jdk1.8.0_201</code>不需要具体到bin目录，之前安装jdk的时候配置环境变量从来不按照网上的JAVA_HOME来配置，都是直接把/bin加入到环境变量，Tomcat让我知道了原来JAVA_HOME的作用在这里，具体配置方式百度。
## service.bat 安装服务
Tomcat还配有图形化启动界面，在/bin目录下，名为<code>tomcat*w.exe</code>(*是你tomcat版本所代表的数字)，点击提示服务未安装，不要慌，打开命令行进入到<code>Tomcat/bin/</code>目录下键入
```
service.bat install
```
然后再点之前的exe文件就能启动了
### 点击Strat之后进度条读一半就结束了状态还是Stop
使用.bat文件启动正常，图形界面就不行，还是查看日志文件，在<code>commons-daemon.xxxx-xx-xx.log</code>中发现
> %1 不是有效的 Win32 应用程序。  

原来是是java虚拟机是64位而Tomcat我下载的是32位,所以不行，更换位32位的jdk或者64位的tomcat即可。
## localhost:8080 127.0.0.1:8080
localhost:8080访问不了127.0.0.1:8080能访问，建议换个浏览器试试