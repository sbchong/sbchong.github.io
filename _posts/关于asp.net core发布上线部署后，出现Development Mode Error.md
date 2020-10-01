# 关于asp.net core发布上线部署后，出现Development Mode Error

## 问题出现

在博客开发过程中，出现过一次**Development Mode Error**事故，访问其他页面都可以正常访问得到，唯独文章详情页面，不论点击哪一篇文章，都会弹出如下所示的错误提示信息。

```
Development Mode Error though environment was set to Production
Apr 06, 2020 07:44 PM|LINK

Hi guys,

I had published my project to hosting successfully several times before adding localization settings to my project. In launchSettings.json file ASPNETCORE_ENVIRONMENT was set to "Production" mode. But after adding localization settings to startup file, creating resource files and updating views accordingly led to following error after publish:

An error occurred while processing your request.
Request ID: 0HLLQOTG6FT8G:00000001

Development Mode
Swapping to Development environment will display more detailed information about the error that occurred.

Development environment should not be enabled in deployed applications, as it can result in sensitive information from exceptions being displayed to end users. For local debugging, development environment can be enabled by setting the ASPNETCORE_ENVIRONMENT environment variable to Development, and restarting the application.
```

## 问题解决

在[微软asp.net论坛](https://forums.asp.net/)上找到了同款问题，看了一下下面的讨论，有人说是这是因为程序出错了导致的。我立即打开VS连到生产数据库调试一下代码，果然是在查询数据库的时候，出现了错误，但由于是生产环境，不会把错误信息展示出来，所以提示我们切换到开发模式（Swapping  Development  Mode）



