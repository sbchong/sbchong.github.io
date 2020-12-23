# Blazor 文件上传解决方案

## 前言

Blazor 是由 Asp.Net Core 团队推出的一个Web前端SPA解决方案，其中包括了使用 WebAssembly 的 Blazor Wasm 和使用 SignalR 进行实时交互的 Blazor server。本篇文章中使用的是 Blazor Wasm 方案。

## 上传文件

在网络应用程序中，您有时需要将文件上传到服务器。这是使用 Blazor 的方法。

目前，Blazor 并没有提供开箱即用的功能，我们将不得不使用 js 加载文件内容，并使用 C＃ 将该内容发送到服务器。

### Blazor

您需要一个选择文件的 input 元素，并使用 Blazor/C＃ 来处理他的 onchange 事件。

我们将从 javascript 函数接收二进制文件作为 base64字符串，因为字节数组无法在 json 中序列化，并且这是 C＃ 和 js 之间的交换格式。

本实例中服务器 Webapi 只会返回保存文件的文件名，但是您可以添加其他想要返回的信息。

```C#
//依赖注入 JSRuntime
@inject IJSRuntime JSRuntime;

<input type="file" onchange="@UploadFile" id="fileUpload" @ref="fileUpload" />

<img src="@ImageSource" alt="已选择图片">

@code {
    public string ImageSource{ get; set; }

    async Task UploadFile()
    {
        // JSRuntime 实现 .Net 调用 Javascript 方法
        var data = await JSRuntime.InvokeAsync<string>("readUploadedFileAsText", fileUpload);
        //js 返回内容为：文件信息+文件 base64 字符串。将其设置为 img 标签的 src，可以预览上传的图片，上传时只需要 base64 字符串
        ImageSource = data;
        //这里直接使用 ByteArrayContent 作为 HttpBody
        var response = await Http.PostAsync("/api/upload",new ByteArrayContent(Convert.FromBase64String(data.Split(",")[1])));
        var fileTempName = await response.Content.ReadAsStringAsync();
    }
}
```

### Javascript

添加一个获取文件内容的方法，Blazor 中调用异步 js 方法，因此我们使用 Promise 来实现 js 中异步读取文件。

Blazor 和 JavaScript 互操作可以参考 MSDN 文档：[在 ASP.NET Core Blazor 中从 .NET 方法调用 JavaScript 函数](https://docs.microsoft.com/zh-cn/aspnet/core/blazor/call-javascript-from-dotnet?view=aspnetcore-3.1)

js 读取文件内容可以参考 MDN 文档：[FileReader的使用](https://developer.mozilla.org/en-US/docs/Web/API/FileReader)，它很有帮助，除此之外还需要了解一下 Promise。FileReader 的 readAsDataURL 方法将文件信息追加到文件 base64 中，其实我们需要上传的只是后面的 base64 字符串。

```javascript
window.readUploadedFileAsText = (inputFile) => {
    const temporaryFileReader = new FileReader();
    console.log(inputFile.files[0])
    return new Promise((resolve, reject) => {
        temporaryFileReader.onerror = () => {
            temporaryFileReader.abort();
            reject(new DOMException("读取文件失败"));
        };
        temporaryFileReader.addEventListener("load", function () {
            resolve(his.result);
        }, false);
        temporaryFileReader.readAsDataURL(inputFile.files[0]);
    });
};
```

### 服务器 Webapi

这是用于将文件内容保存在服务器上的代码，将二进制数组从 HttpBody 流里读取然后写入文件中。

```C#
[Route("api/upload")]
    public class UploadController : ControllerBase
    {
        [HttpPost]
        public async Task<IActionResult> Post()
        {
            var tempFileName = $"../WebProgramPath/{Guid.NewGuid().ToString()}";
            using (var writer = System.IO.File.OpenWrite(tempFileName))
            {
                await Request.Body.CopyToAsync(writer);
            }
            return Ok(Path.GetFileNameWithoutExtension(tempFileName));
        }
    }
```

## 残留问题

使用上面的代码，可以实现 Blazor 客户端上传文件到服务器，但在这个过程中我也发现，当选择的文件超过一定大小时，会引发错误：

<font color=red>
**Uncaught (in promise) RuntimeError: memory access out of bounds。**
</font>

这可能由于 wasm 和 js 互操作时的共享内存规定所导致的，网上解决方案时在 wasm 打包时指定内存，但 Blazor 中没有找到合适的解决方案。

所以，我在 GitHub 提了一个 issue 给 Asp.Net Core 团队，他们回复我：团队将会在 RC1 版本中添加一个新的组件——InputFile，来避免在文件操作时开发者进行繁杂的 Js 互操作。
