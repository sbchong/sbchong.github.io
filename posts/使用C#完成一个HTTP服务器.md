# 使用C#完成一个HTTP服务器

## 介绍

### HTTP

HTTP是计算机网络中的一个应用层协议

### TCP/IP

TCP/IP通信

### socket

socket是一个操作系统对`TCP/IP`通信的抽象层，本身计算机网络中不存在该层

## 开始

使用socket完成一个网络应用分为：绑定，监听，收，发四步走

### 绑定

首先先绑定地址和端口，以常用的HTTP服务器来说明，一般HTTP服务器都默认绑定本机所有地址和80端口

```C#
public void Run()
{
    IPEndPoint ipe = new IPEndPoint(IPAddress.Any, 80);
    TcpListener tcpListener = new TcpListener(ipe);
    tcpListener.Start();

    Accept();
}
```

### 监听

绑定好地址和端口后，就是不断监听该地址端口，等待访问

本文使用递归模式，也可以采用死循环模式

```C#
public void Accept()
{
    try
    {
        tmpTcpClient = tcpListener.AcceptTcpClient();

        if (tmpTcpClient.Connected)
        {
            var clientId = Guid.NewGuid();
            HandleClient handleClient = new HandleClient(clientId, tmpTcpClient, this);
            new Thread(Request) { IsBackground = true }.Start();
        }
    }
    catch (Exception ex) { Console.WriteLine(ex.Message); }

    Accept();
}
```

### 接收请求

一旦建立连接，则开始接收请求

```C#
public void Request()
{
    string request = string.Empty;
    byte[] receiveBytes = new byte[tmpTcpClient.ReceiveBufferSize];
    int numberOfBytesRead = 0;
    NetworkStream ns = tmpTcpClient.GetStream();

    if (ns.CanRead)
    {
        do
        {
            numberOfBytesRead = ns.Read(receiveBytes, 0, tmpTcpClient.ReceiveBufferSize);
            request += Encoding.UTF8.GetString(receiveBytes, 0, numberOfBytesRead);
        }
        while (ns.DataAvailable);
     }

     Process(request)
}
```

### 解析请求

接收到的请求字符串进行解析，通过分析HTTP包结构来处理

先来复习一下HTTP报文结构

整个HTTP请求报文分为两部分，一个是请求头，一个是请求体

```C#

```
