# Asp.Net Core应用配置监听端口

## 前言

一般情况下，asp.net core应用发布后的默认端口是5000，默认代码中没有任何可以让你输入的ip地址和端口号。但在开发过程中，我们总是需要对端口进行动态地调整，不希望被框架所束缚，那如何实现默认端口的修改呢

## 配置方法

### 通过UseUrls()配置

IWebHostBuilder中可以很轻松地找到UseUrls()方法，注释也说明该方法用于配置应用绑定的url。

```C#
public class Program
{
    public static void Main(string[] args)
    {
        CreateWebHostBuilder(args).Build().Run();
    }

    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
                .UseUrls("http://*:9999") //配置应用可以绑定任意IP地址，并监听9999端口
                .UseStartup<Startup>();
}
```

将Program.cs修改为上面的代码，启动后即可发现应用开始监听9999端口。


使用类似于栈的思路，可以采用一个 string 或者 vector<char> 临时变量，（也可以用指针）

1，从第一个字符开始循环，判断当前字符是否为 '('，'{'，'[ ，若为左括号，则将其写入到临时变量中。

2，若当前字符为右括号，则判断临时变量最后一个元素是否与其配对，若不配对，则直接返回false。若配对，则删除临时变量中的最后一个元素，继续循环。

3，若正常循环结束，则判断临时变量的大小是否为0，不为0则说明右括号不匹配，返回false。为0则说明匹配，返回true。