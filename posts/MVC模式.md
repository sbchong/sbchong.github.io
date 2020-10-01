# 浅析web前端中的MVC模式

MVC是常见的软件架构设计模式，它通过分离关注点改进代码的组织方式。区别于软件设计模式，只是为了解决问题总结出的抽象方法，一种架构模式种往往会用到多种设计模式。

![preview](https://pic3.zhimg.com/v2-eb798662fe9da500582ff111dd4235b2_r.jpg)

图为知乎中大佬的配图，图中实线表示调用，虚线表示事件通知。

MVC模式允许在不改变视图的情况下改变视图对于用户的输入响应，即用户对View的操作全提交到Controller中进行处理，在Controller中响应View的事件调用Model的接口对数据进行操作，Model中数据发生变化则通知相关的视图进行更新。

下面用一个简单的小例子，该例子中页面上有一个记录点击次数的p标签，和一个按钮，每点一次按钮，点击次数就进行加一操作。

## Model

Model层主要用来存储业务数据以及相关操作，当数据发生变化是，通知有关的View进行更新。

```javascript
let count = new Object()

 count.value = 0

 count.views = []

 count.updateViews = []

 count.getVal = () => { return count.value }

 count.setVal = val => {
     count.value = val
 }

 count.addVal = () => {
     count.value++
 }

 count.register = views => {
     if (views == null || views == 'undefined' || views.length <= 0) {

     } else {
         let len = views.length
         for (let i = 0; i < len; i++) {
             count.views.push(views[i])
         }
     }
 }

 count.notify = updateViews => {
     let len = 0
     if (updateViews == null || updateViews == 'undefined' || updateViews.length <= 0) {
         len = count.views.length
         for (let i = 0; i < len; i++) {
             count.views[i].render(count)
         }
     } else {
         count.updateViews = updateViews
         len = count.updateViews.length
         for (let i = 0; i < len; i++) {
             count.updateViews[i].render(count)
         }

     }
 }

 export { count as Count }
```

在Model和View之间使用观察者模式，程序开始时，在Model中注册相关的View，然后观察Model，当数据发声变动时及时更新View。

## View

View中主要负责思考怎么将Model的数据渲染到页面上。

```javascript
import { el } from "../common/querySelector.js"

let countView = new Object()

countView.count = el('#count')

countView.btn = el('#countBtn')


countView.render = model => {
    countView.count.innerText = model.getVal()
}

export { countView as View }
```

## Controller

控制器是模型和视图之间的纽带，MVC将响应机制封装在controller对象中，当用户和你的应用发生交互时，控制器中的事件触发器就开始工作了。

```javascript
import { Count } from '../models/count.js'
import { View } from '../views/countView.js'

export const countController = new Object()

let count = null
let views = []

countController.init = () => {
    count = Count
    views.push(View)
    views[0].btn.addEventListener('click', countController.increse)
    count.register(views)
    count.notify()
}

countController.increse = () => {
    count.addVal()
    count.notify(views)
}
```

将所有的View给到对应的Model中进行注册，当Model发生变化时就去通知View做更新。

当我们程序开始时，应该先初始化Controller。

```javascript
import { countController } from './controllers/countController.js'

window.onload = () => {
    countController.init()
}
```

可以看到，当我们点击页面上的‘点击’按钮时，上方的数字会依次增加。

当进行抽象分离后，主要的业务逻辑都集中到Controller中，View只负责页面渲染，Model负责数据更新。所以，当业务逻辑增加时，Controller就会变得**十分臃肿**，再加上View和Controller的紧密联系，使得业务逻辑即使是很小的变动，也必须修改新的Controller使得难以复用。

