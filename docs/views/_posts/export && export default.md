---
  title: 关于export和export default的那些事
  date: 2020-01-15
  tags:
      - vue
      - ES6
  auth: xya370
  categories:
    - 踩坑日常
---

<!-- # 关于export和export default的那些事 -->
  最近，在写一个项目，其中一个页面data中的基本数据略多，于是想要拆分出来，从外部引用，在js中使用export default时会报错，改成export就可以了，所以就有了这篇文章

  关于export 和export default 我们简单知道的就是这两个都是暴露模块的一种方式，但是具体有什么区别呢。

## export
  export 是通过将指定的方法，对象等暴露出去，通过import可以被外部引用

    基本语法：
      let obj = {};
      let array = [];
      let string = "124";
      export {obj, array, string}
      //
      export function (){};
      //
      let people = {
        name: "lisi",
        age: "23",
      }
      export * from people;
      export { name } from people

  以上是几个常用的基本语法，具体的可以查看 [文档:](https://developer.mozilla.org/zh-TW/docs/Web/JavaScript/Reference/Statements/export)

## export default
  export default 是export的一种使用方式，预设输出，每个模块中只能有一个export default。
  export default在vue代码中比较常见，可以使用任意名字进行输出

    示例：
      // a.vue
      export default {
        name: "a"
      }

      // b.vue
      import k from "./a.vue"

      export default function(){}

  **需要注意，export default 不可使用let,const,var**

## 不同之处
  1. **在一个文件中输出模块时，export可以输出多个模块，但是只能输出一个export default。**

    a.js
      export {
        obj: {},
        array: []
      }

      export default function(){}

  2. **export 更加适合输出多模块，后面可接大括号,export default只能输出一个模块，且不可接大括号**。<br>
  3. **从对模块的引用来看，export中的模块被引用时，模块名称必须保持一致，而export default 则可以使用任意名称**

    b.vue
      import k  from "./a.js"
    c.vue
      import { obj } from "./a.js"