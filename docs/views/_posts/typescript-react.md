---
    title: typescript 初体验
    date: 2020-05-09
    tags:
        - typescript
        - react
    auth: xya370
    categories:
        - 挖坑中
---

<!-- # typescript 初体验 -->
## 普通的typescript文件使用
    默认已全局安装typescript;
    typescript 文件是无法直接引入html文件中被浏览器编译的，因此需要通过编译为普通的js文件在浏览器中使用。
    cd 到typscript文件目录下
    tsc [fileName].ts
    通过tsc命令编译文件

## typescript && react 项目引入
### 依赖安装

    1. 全局安装 react typescript

            npm install typescript -g
            npm install create-react-app -g

    2. 生成
        - 新项目： npx create-react-app demo -typescript || npx create-react-app demo -template typescript
        - 已有react项目添加：
                npm install typescript --save-dev
                npm install @types/react @types/react-dom --save-dev
                根目录下创建tsconfig.js文件, 并将文件后缀改为.tsx;
                可以直接使用tsc --init 命令进行生成， 也可以直接添加文件

            ```
                tsconfig.js

                {
                    "complierOptions": {
                        "target": "es5",
                        "module": "commonjs",
                        "jsx": "react",
                        "sourceMap": true,
                        "strict": true,
                        "noImplicitAny": false,
                        "forceConsistentCasingInFileNames": true  ,/* Disallow inconsistently-cased references to the same file. */
                        "skipLibCheck": true,
                        "allowSyntheticDefaultImports": true,
                        "paths": {
                            "@App": ["./src/app"],
                            "@Compontents/*": ["./src/components/*"]
                        }
                    },
                    "include": ["./src"]
                }
            ```

            关于tsconfig.js文件的具体配置内容可以查看[官方文档](https://www.tslang.cn/docs/handbook/tsconfig-json.html)

## 项目进行中：

### 项目中使用@修饰符进行模块文件查询

    1. npm run enject 展开config等文件
    2. ./config/webpack.config.js 文件中添加内容：

        alias: {
            '@': paths.appSrc
        }
    3. tsconfig.js 文件中添加内容：

        {
            "compilerOptions": {
                "baseUrl":"./",
                "paths": {
                  "@/*": ["src/*"]
                }
            }
        }
    4. 重启项目，进行文件的引入。

### 项目中引入swipper

    1. npm install @types/swipper
    2. 文件中使用swipper,并引入css

        import Swiper from 'swiper';
        import 'swiper/css/swiper.min.css';
    3. componentDidMount 中对swiper进行初始化

#### 在react项目中使用ref
非typescript环境下：

    render () {
        return (
                <div>
                    <input ref = {(ele) => {this.inputDom = ele}}/>
                </div>
            )
    }

这是一般情况下的ref使用方式，但是如果在typescript的环境下这么写的话，由于typescript的机制是会报错的，我们需要预先声明ref的类型

typescript环境下：

    export class DefineComponent extends Component {
        private refDom: HTMLDivElement;
        constructor(props:Object) {
            super(props);
            this.refDom = document.createElement("div");
            // 在这里先给ref一个初级变量，不然后面获取相关dom的信息时会报错
        }
        render () {
            return (
                <div ref = {(ele:HTMLDivElement) => {
                    this.refDom = ele;
                    }}></div>
                )
        }
    }

### typescript-react && axios && http代理
 在项目中使用axios和其他项目一样，都是直接 npm install axios,然后在需要的文件中import引入进行使用，但是一般我们在项目中使用的都是进行过封装的axios方法，
 在这里，同样先对axios方法进行封装：

##### axios封装：
    httpClient.ts

``` javascript
// 简单写一下
    import axios from 'axios';
    const server = axios.create({
        timeout: 10000,
        headers: {
         common:{
           'Access-Control-Allow-Origin': "*"
         }
        }
    }),
    defaultParams = {
        token: ""
    }
    // axios 拦截器重点
    server.interceptor.request.use(
        (config:any) => {
            // 可以在这里进行操作，添加token等
            // 添加默认参数
            if (config.method.toLowerCase() === 'get') {
                config.params = Object.assign(config.params, defaultParams)
            }
            if (config.method.toLowerCase() === 'post') {
                config.data = Object.assign(config.data, defaultParams)
            }
            return config
        },
        (error:any) => {
            Promise.reject(error)
        }
    )
    server.interceptor.response.use(
        (response:any) => {
            if (response.status !== 200) {
                // 可根据不同的返回状态进行判断；
                return Promise.reject('error')
            } else {
                return response.data
            }
        },
        (error:any) => {
            var originalRequest = error.config;
            if (
              error.code === 'ECONNABORTED' &&
              error.message.indexOf('timeout') >= 0
            ) {
                // 当请求超时重新请求，
                // 不建议无限请求，最好设置一个请求次数限制
              originalRequest._retry = true
              return server.request(originalRequest);
            } else {
              return Promise.reject(error.message || '系统错误，请刷新后重试');
            }
        }
    )
    const GET = (url:string, params:any = {}) => {
        return server({
            url,
            params,
            method: 'GET'
        })
    }
    const POST = (url:string, data:any = {}) => {
        return server({
            url,
            data,
            method: 'POST'
        })
    }
    export {GET, POST}
```

##### axios 使用
这里我是将所有api放一个文件中，然后传出去的。

    apiList.ts
```javascript
    import { GET } from './httpClient';
    const _getInfo = (id:string) => {
        return GET('movie/subject/'+id,{})
    }
    export {_getInfo};
```

### 项目中的反向代理proxy
在前后端分离时，不想设置axios的baseUrl来进行请求，我们使用proxy来进行代理

方法一：

    网上搜的方法，在package.json中进行设置
    {
        "proxy": "https://douban.uieee.com/v2/"
    }
    这个方法可能是我哪里没有弄好，并不怎么有用

方法二：

    在create-react-app 的文档中说明可以使用http-proxy-middleware来进行设置。
    * npm install http-proxy-middleware
    * 在src目录下新建setupProxy.js
        setupProxy.js
```javascript
        // 注意http-proxy-middleware的版本号
        // 1.0版本
        const { createProxyMiddleware } = require('http-proxy-middleware');
        module.exports = function(app) {
          app.use(createProxyMiddleware('/v2/*', {
               target: "https://douban.uieee.com/v2",
               secure: false,
               ws: true,
               changeOrigin: false,
              //  headers: {
              //     "Connection": "keep-alive"
              // },
              https: true,
            })
          );
        };

        // 1.0以下版本
        const proxy = require('http-proxy-middleware');
        module.exports = function(app){
            app.use(proxy('/api/*',{
                target: ''
            }))
        }
```
最后记得重启下服务哟

### typescript && react 中的window扩展添加全局变量
有时需要设置全局变量，但是在引用了typescript后由于限制严格，不能随意在window中添加对象，直接使用global.x会提示报错，
方法: 在使用tyescript时会生成一个全局的声明文件.d.ts, 可以在里面添加声明，如global.loadShow,

``` typescript
   declare namespace NodeJS {
      interface Global {
          loadShow:Boolean
      }
    }
```
这样我们就可以在需要的文件中直接global.loadShow使用了