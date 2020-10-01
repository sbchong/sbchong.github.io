#  关于asp.net core webapi返回415状态记录

## 问题

在开发博客系统的webapi接口时，获取博客文章列表的接口出了状况，直接返回了415状态，打断点发现，根本没有进路由的Get方法，不禁虎躯一震，连调试都做不到，还要我咋样。

我甚至还怀疑是浏览器有问题，相继更换**Microsoft Edge**，**Mozilla FireFox**，**Google Chrome**，都是一样的结果，不信邪，我又使用**Nighting**和**Postman**测试，结果也是一样的，根本不会进入控制器 的Get方法，直接返回了415状态。**事实证明，代码写错了就是错了，不关测试工具任何事。**

在这个问题出现后的两天里，简直是精神折磨，只要一打开项目就想起这个问题，其他模块的开发瞬间也没了兴趣，~~还不如打两局LOL克隆模式开心~~。这就误让我以为所有的接口都有这种问题，直接导致了我把这个问题的锅甩给了asp.net core，查阅了很多博客，**StackOverflow**关于返回415状态的问题看了又看，**MSDN**逛了又逛，**GitHub**的issue都翻了遍，还是没得一点头绪。

## 解决

在经历了两天的折磨之后，准备将代码同步到Azure DevOps进行编译时，突然想起了在asp.net core webapi中，Get方法的参数一般都是从地址链接中获取，一般都会使用 [**[FromQuery]**](https://docs.microsoft.com/zh-cn/dotnet/api/microsoft.aspnetcore.mvc.fromqueryattribute?view=aspnetcore-3.1) 特性来描述，赶紧在代码中加上，运行，访问，然后，就可以成功获取到数据了。

具体原因，大概就是我的查询参数类没有指定绑定来源，asp.net core无法进行验证绑定，所以直接返回了415：Unsupported Media Type。

 

