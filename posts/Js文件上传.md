# Js 文件上传

## 前言

文件上传是一个前端比较常见的功能，无论是以前的MVC客户端，还是现代化SPA客户端中，但万变不离其宗，其内核基本不变，本文就此讨论简单文件上传（以图片为例子）。

解决问题：

- 使用js上传一张图片

- 使用js选择一张图片预览，再上传
- 使用js上传多张图片



## mvc上传图片

mvc客户端可以直接使用form表单上传，指定表单提交内容类型为 **enctype="multipart/form-data"**即可。

```html
<form action="uplaod" method="POST" enctype="multipart/form-data">
  <input type="file" name="select">
  <input type="submit">
</form>
```



##  使用js上传一张图片

### 图片选择

使用js来上传页面上还是使用input元素，需要使用到input元素的change事件。

```html
<input type="file" class="select" id="select" />
```

给input绑定change事件，可以在事件传递中获取到选择图片对象。不过因为这个对象比较特殊，所以这个对象是不能直接看见的。

不过，这个对象有一个files属性，对应了选中的图片，是一个FileList对象，FileList对象和数组一样有一个length属性，但没有数组的其他特性。可以像数组一样使用index（索引）来获取子对象和使用length（长度）来循环。

```javascript
document.querySelect('#select').addEventListener('change', e => {
    //直接打印出选中的第一个图片对象
	console.log(e.target.files[0])
    //判断是否有文件
    if(!e.target.files) return 
    //上传单个图片
    upload(e.target.files[0])
})
```



### 图片上传

拿到了选中的图片，下一个就是上传给后端。需要使用到**FormData**对象。使用FormData的append方法将单个图片对象放到FoemData中。建议使用三个参数的append方法，也可以使用两个参数。

```javascript
const upload = file => {
    let formData = new FormData()
    //添加图片到FormData对象，参数分别为key，图片对象，图片名
    formData.append('upload', file, file.name)
    //上传，Jq/Axios/HttpClient随意
    ...
}
```



## 使用js预览文件再上传

相比于上面的直接上传，大家肯定都喜欢先本地预览图片，再确定是否上传。当然你完全可以先上传图片到服务器做临时文件来预览，用户确认后再持久化。对此只能说：**可以，但没必要**。

### 图片预览

在上面的基础上，再加上一个img标签来预览图片。

```html
<input type="file" class="select" id="select" />
<img src="" alt="" id="preview"/>
```

使用js将文件转为base64字符串，再用img来预览。

```javascript
const previewFile = file => {
	let temporaryFileReader
    if(file){
        //创建对象流，转换时给base64字符串加上特性URL
        temporaryFileReader = new FileReader()
        temporaryFileReader.readAsDataURL(file)
    }
    //转换完毕，将字符串作为img标签的src属性值
    temporaryFileReader.addEventListener('load', e => {
		document.querySelector('img').src = e.target.result
    }, false);
}
```



## 上传多张图片

给input加上**multiple**属性即可同时选中多张图片。

```html
<input type="file" class="select" id="select" multiple/>
```

### 图片上传

FormData的append方法会智能地根据key来追加内容，如果需要查看同一个key下所有文件对象，需要使用getAll方法，get方法默认获取第一个对象。

```javascript
//多图片上传需要使用同一个FormData对象，提升作用域，避免追加失败
let formData = new FormData()

document.querySelect('#select').addEventListener('change', e => {
    //直接打印出选中的第一个图片对象
	console.log(e.target.files[0])
    //判断是否有文件
    if(!e.target.files) return 
    //循环上传多个图片
    for(let i = 0; i < e.target.files.length; i++)
    	upload(e.target.files[i])
})
    
const addFile = file => {
    //添加图片到FormData对象，参数分别为key，图片对象，图片名
    formData.append('upload', file, file.name)
    //每次执行formData内部文件都会加1
    console.log(formData)
}

//上传，Jq/Axios/HttpClient随意
 ...
```

**要预览多张图片，可以使用js来直接追加内容或者创建img标签，如果是SPA，可以直接使用数据绑定和列表渲染，将转换的base64字符串直接循环绑定到img的src属性**。

## 总结

总的来说，就是调用API（DOM对象事件监听，文件流，还有就是HTTP请求）。本文讨论的上传图片，还可以使用file对象的属性判断文件类型和大小，完成一些限制。

