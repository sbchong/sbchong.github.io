# C# 使用 HttpClient 进行 POST 请求

## 前言

前一篇文章介绍了 HTTPClient 以及使用其进行 HTTPGET 请求，本篇我们来看一下使用 HttpClient 进行 POST 请求。

## 介绍

一般来说，查询，搜索等操作一般都可以使用 GET 请求来完成，但对于数据操作还有一些重要的查询需要使用 POST，比如用户注册是创建数据，但用户登录却是查询数据，但同样的都是使用 POST 完成。

