---
    title: react-loading
    date: 2020-05-22
    tags:
        - react
    auth: xya370
    categories:
        - 挖坑中
---
    鉴于每次请求的时间过长，需要在请求时添加一个loading,减缓用户焦虑，

## component-loading
    新建一个loading组件

``` typescript
    // loading.tsx
    import React, {Component} from 'react';
    import './index.scss';
    export class Loading extends Component <{show:Boolean},{}> {
        render () {
            return (
                this.props.show && <div className="loading">
                  <div className ="spinner_cell">
                    <div className="spinner">
                      <div className="double-bounce1"></div>
                      <div className="double-bounce2"></div>
                    </div>
                    <div className="spinner next">
                      <div className="double-bounce1"></div>
                      <div className="double-bounce2"></div>
                    </div>
                    <div className="spinner last">
                      <div className="double-bounce1"></div>
                      <div className="double-bounce2"></div>
                    </div>
                  </div>
                </div>
            )
        }
    }
```

```scss
    .loading {
      width: 100%;
      height: 100%;
      position: fixed;
      top: 0;
      bottom: 0;
      right: 0;
      left: 0;
      @include styles("background-color", "load")
      z-index: 2;
    }
    .spinner_cell {
      position: absolute;
      top: 0;
      left:0;
      bottom: 0;
      right: 0;
      margin:auto;
      background: transparent;
      width: fit-content;
      height: fit-content;
    }
    .spinner {
      width: 20px;
      height: 20px;
      position: relative;
      display: inline-block;
      animation: bounce 2.0s infinite ease-in-out;
      &:not(:last-child) {
       margin-right: 20px;
      }
      .double-bounce1 {
        width: 20px;
        height: 20px;
        @include styles("background-color","active")
      }
      .double-bounce2 {
        width: 30px;
        height: 30px;
        position: absolute;
        top:-5px;
        right: -5px;
        border: 1px solid;
        @include styles("border-color", 'active')
      }
      .double-bounce1, .double-bounce2 {
        border-radius: 50%;
      &.next {
        animation-delay: 1.0s;
      }
      &.last {
          animation-delay: 1.5s;
      }
    }
    @-webkit-keyframes bounce {
      0%, 100% { -webkit-transform: scale(0.0) }
      50% { -webkit-transform: scale(1.0) }
    }

    @keyframes bounce {
      0%, 100% {
        transform: scale(0.0);
        -webkit-transform: scale(0.0);
      } 50% {
        transform: scale(1.0);
        -webkit-transform: scale(1.0);
      }
    }
```
这样就完成了一个loading组件，现在我们需要的是怎么使用的问题。

## 使用
   可以在app.js中引用loading, 同时设置全局的loadShow,以loadShow来控制loading的展示。如果是单个页面使用，可以直接引入load组件，但是我想要的是在请求的时候进行展示，并且并不希望在每个页面里都写一遍load的控制，我们可以在请求的拦截器里进行loading的处理。首先，我们需要再处理一下loading。
   新建一个load.js文件

   ```javascript
        import React from 'react';
        import ReactDOM from 'react-dom';
        import {Loading} from '@/components/loading'
        let loadingShow = false
        // 计数器，
        let loadCount = 0;
        let load = {
          open: () => {
            loadCount ++; // 记录多个请求时的次数
            // 使用render将创建的模板插入指定的DOM节点
            ReactDOM.render(
              <Loading show={loadingShow}/>,
              document.getElementById('load')
            );
            loadingShow = true
          },
          close: () => {
            loadCount --
            if (loadCount === 0 ){
              // 使用ReactDOM.unmountComponentAtNode移除指定DOM中加载的react组件
              ReactDOM.unmountComponentAtNode(document.getElementById('load'));
            }
          }
        }

        export default load
   ```
  封装好了之后，我们就可以在请求的拦截器里使用了。记住，要在app.js里添加一个#load的div。

  httpClient.ts

  ```typescript
    import axios from 'axios';
    import load  from './loading.js';
    ...
    axios.interceptors.request.use((config:any)=>{
      load.open();
      ...
      return config
    },(error:any) => {
      load.close();
    })
    axios.interceptors.response.use((reponse:any) => {
      load.close();
      ...
    },(error:any) => {
      load.close();
      ...
    })
  ```

  这样一个在请求时自动加载的loading就好了
