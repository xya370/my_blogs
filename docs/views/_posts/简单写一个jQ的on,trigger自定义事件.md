---
  title: 简单写一个jQ的on,trigger 自定义事件
  date: 2020-08-21
  tags:
      - js
  auth: xya370
  categories:
    - 挖坑中
---

# 简单写一个jQ的on,trigger方法
我们一般绑定事件，都是使用已有的事件进行绑定，但是有时我们会需要绑定一个自定义事件，这时候使用jq就很简单了。

``` javascript
    $(dom).on('customEvent', fn);
    $(dom).trigger('customEvent')
```
但是，如果使用原生js,我们需要怎么做呢？这里需要介绍两个方法： CustomEvent, dispatchEvent

## customEvent
customEvent 是一个构造函数，用来构建一个自定义事件。

    基础用法： event = new CustomEvent(typeArg, option);
    option: {
        detail: '' // 传参？
        bubbles: false // 是否冒泡
        cancelable: false // 是否可以取消
    }

## dispatchEvent
dispatchEvent, 向指定目标派发一个事件，并在适当的时候执行该事件的回调函数。

    cancelled = target.dispatchEvent(event)

## 开始
    基于上面两个方法，我们可以使用原生js来模拟一下jq中的自定义事件，以及trigger。

``` javascript
    class customEf () {
        constructor(dom) {
            this.dom =  document.querySelector(dom);
            //  在原型链上添加事件
            Element.prototype.on = this.on;
            Element.prototype.customEvents = [];
            Element.prototype.trigger = this.trigger;
            return this.dom;
        }
        on (typeArg, fn) {
            //  addEventListener 监听事件
            this.addEventListener(typeArg, fn);
            let event =  new CustomEvent(typeArg)
            this.customEvents.push(event)
        }
        trigger (eventName) {
            let customEvents = this.customEvents;
            let event = customEvents.filter(_ =>{ return _.type === eventName})
            if (event.length > 0) {
                this.dispatchEvent(event[0])
            }
        }
    }

    使用：
    let cE = new customEf ('.test');

    cE.on('myEvent', (e) => {
        alert('myTrigger')
    })
      // cE.addEventListener('click',() =>{
    cE.trigger('myEvent')
```
这只是一个基础的方法，更深层次的需要再研究一下