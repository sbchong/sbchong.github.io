# C# 中 HttpClient 的简单使用

## 介绍

当我们在开发客户端还有少部分服务端程序时，数据需要从 Webapi 服务器取得。在以前我使用的比较多的时 [WebClient](https://docs.microsoft.com/zh-cn/dotnet/api/system.net.webclient?redirectedfrom=MSDN&view=netcore-3.1) 和 [HttpWebRequese](https://docs.microsoft.com/zh-cn/dotnet/api/system.net.httpwebrequest?redirectedfrom=MSDN&view=netcore-3.1)，在新的 .Net 版本中，团队为其带来了更多的可选择性——新的 [HttpClient](https://docs.microsoft.com/zh-cn/dotnet/api/system.net.http.httpclient?view=netcore-3.1)。它有着更加灵活的 API。发送请求和接收响应主要是通过 HttpRequestMessage 和 HttpResponseMessage 这来给你个类，通过属性 Content 获取 http 响应内容。

## 区别

HttpClient 与 WebClient 和 HttpWebRequest 相比，有以下几个主义的

1. HttpClient 实例初始化时不与服务器域名或者主机地址绑定，也就是說可以通过一个 HttpClient 实例发送多个不同的请求（比如同一个 HttpClient 请求 api.a.com 和 api.b.com 两个服务器数据）
2. HttpClient 通过设置 DefaultHeaders 设置 HttpHeader 并且应用于该实例的所有请求
3. HttpClient 使用可以很容易的处理异步请求（Async / Await）

## 使用方法

HttpClient 提供有与 Http Method 匹配的方法，比如 GET / POST / PUT ...等。

先实现模拟一个 GET 请求，代码如下

```C#
// 传入参数 url 为请求地址
async Task Get(string uri = "http://api.domain.com/search";)
{
    // 使用 using 语法建立一个明确周期的 HttpClient 实例
    using (HttpClient client = new HttpClient())
    {
        try
        {
            // 设置超时限制，有效防止浪费资源
            client.Timeout = TimeSpan.FromSeconds(30);
            //使用 GetAsync 进行异步 HTTPGET 请求
            HttpResponseMessage response = await client.GetAsync(uri);
            // 判断服务器响应代码是否为 2XX
            response.EnsureSuccessStatusCode();
            //使用 await 语法读取响应内容
            string responseBody = await response.Content.ReadAsStringAsync();
 
            Console.WriteLine(responseBody);
        }
        catch (HttpRequestException e)
        {
            Console.WriteLine("Error Message :{0} ", e.Message);
        }
    }
}    
```

除了直接使用与 Http Method 对应的方法外，还可以单独构建 HttpRequestMessage 实例进行请求

```C#
async Task Get(string uri = "http://api.domain.com/search";)
{
    using (HttpClient client = new HttpClient())
    {
        HttpRequestMessage httpRequest = new HttpRequestMessage(HttpMethod.Get, uri);
 
        await client.SendAsync(httpRequest)
                .ContinueWith(responseTask =>
                    {
                        Console.WriteLine("Response: {0}", responseTask.Result);
                    });
    }   
}
```

至于 Post 请求，对于初次接触 HttpClient 的人可能显得有些复杂，比较折腾，另一篇文章再讲解。

最后一点，对于不想折腾的人，建议直接使用 RestSharp ，简单好用，上手容易。
