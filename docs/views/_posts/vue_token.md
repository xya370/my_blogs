---
  title: 关于vue中使用token验证登录的那些事
  date: 2020-02-19
  tags:
      - vue
      - axios
  auth: xya370
  categories:
    - 踩坑日常
---

<!-- # 关于vue中使用token验证登录的那些事 -->
  在vue项目中一般使用token进行登录验证，对接口进行请求，返回一串带有身份信息的字符，并在后面的请求中都带上数据
## 基本方式
  * 方式一： 在请求之前进行判断token是否需要更新
  * 方式二： 在请求了之后根据返回判断token是否需要更新

  这里我选择使用第一种方式。
  参考文章
  [axios如何利用promise无痛刷新token](https://segmentfault.com/a/1190000020986592)

## 使用添加
  我们可以将token存储在vuex中，也可以存储在session或localStorage中，这里存储在vuex中
  1. store->index.js

    const store = new Vuex.Store({
      state: {
        // 存储token
        Authorization: JSON.parse(sessionStorage.getItem('Authorization')) ? JSON.parse(sessionStorage.getItem('Authorization')) : '',
        tokenTime: JSON.parse(sessionStorage.getItem('tokenTime')) ? JSON.parse(sessionStorage.getItem('tokenTime')) : '' //存储获取时间，进行对token过期时的判断
      },
      getters: {
        getAuthrization (state) {
          return state.Authorization
        },
        getTokenTime (state) {
          return state.tokenTime
        }
      },
      mutations: {
        changeLogin (state, Authorization) {
          state.Authorization = Authorization
          state.tokenTime = new Date().getTime()
          sessionStorage.setItem('tokenTime', JSON.stringify(state.tokenTime))
          sessionStorage.setItem('Authorization', JSON.stringify(state.Authorization))
        },
        removeLogin (state) {
          sessionStorage.removeItem('tokenTime')
          sessionStorage.removeItem('Authorization')
          state.Authorization = ''
          state.tokenTime = ''
        }
      }
    })

  在基础方法写完后，我们需要在请求时将获取的token放置在请求头中，默认使用axios,利用axios拦截器进行设置

  2. http.js
``` javascript
    import store from './store'

    window.isRefreshing = false;

    axios.interceptors.request.use(
      config => {
        let token = JSON.parse(store.getter.getAuthrization)
        if (token) {
           config.headers.Authorization = 'Bearer ' + token
        }
        let resetTime = JSON.parse(store.getter.getTokenTime)
        if (new Date().getTime() - resetTime >= 0.5 * 60 * 60 * 1000) {
          if (!window.isRefreshing) { // 判断是否正在获取，禁止重复获取
            window.isRefreshing = true
            axios({
              method: 'get',
              withCredentials: true,
              url: '/getToken'
            }).then(res => {
              window.isRefreshing = false
              store.commit('changeLogin', res)
            })
            .catch(err => {
              window.isRefreshing = false
            })
          }
        }
        return config
      },
      error => {
        return Promise.reject(error)
      })
```
  这样一个简单基本的token更新就完成了，但是还需要优化。

## 优化
  上面的代码，仅适合基础使用，我们的页面请求，一个页面下可能会有多个请求，但是异步状态下，返回顺序不定， 如果这个时候token过期，那么就会有问题了，我们希望在使用时，可以无感更新token，这样就需要改变token更新时，其他接口的状态。这样的话，我们的更新机制需要改变为，在更新token时，暂停其他接口，等待token更新后再继续。

  这样，我们修改代码如下：
``` javascript
  // 更新代码单独抽离
  const refreshToken = () => {
    return axios({
      method: 'get',
      withCredentials: true,
      url: '/getToken'
    })
  }
  let requests = [];
  window.isRefreshing = false;
  axios.interceptors.request.use(
    config => {
      let token = JSON.parse(store.getter.getAuthrization)
      let resetTime = JSON.parse(store.getter.getTokenTime)
      if (token) {
        config.headers.Authorization = 'Bearer ' + token
      }
      if (new Date().getTime() - resetTime >= 0.5 * 60 * 60 * 1000) {
          if (!window.isRefreshing) { // 判断是否正在获取，禁止重复获取}
            refreshToken.then(res => {
              window.isRefreshing = false
              store.commit('changeLogin', res)
              return res;
            })
            .then((token) => {
              // 依次执行队列中的请求
              requests.forEach(cb => cb(token));
              requests = [];
            })
            .catch(err => {
              window.isRefreshing = false
            })
          }
          // 将此时多个请求添加进入队列，并重新设置请求token;
          const retryOriginalRequest = new Promise((resolve) => {
            requests.push(token => {
              // 将更新后的token替换原有token;
              config.headers.Authorization = 'Bearer ' + token;
              resolve(config);
            })
          })
          return retryOriginalRequest
      }
      return config;
    },
    error => {
      return Promise.reject(error)
    }
  )
```

主要思想： 在token更新时，使用promise，保持后续接口的pending状态，等到请求完毕再进行执行