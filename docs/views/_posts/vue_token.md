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

    import store from './store'

    axios.interceptors.request.use(
      config => {
        let token = JSON.parse(store.getter.getAuthrization)
        if (token) {
           config.headers.Authorization = 'Bearer ' + token
        }
        return config
      },
      error => {
        return Promise.reject(error)
      })

## 过期判断及刷新
  一般来说，token有一定的时间期限，一般都需要对token进行重新获取

    window.isRefreshing = false
    // 刷新操作
    axios.interceptors.response.use(
      response => {
        let token = JSON.parse(store.getter.getAuthrization)
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
          }
        }
        return response.data
      },
      error => {
        // 假设此时，token失效，需要重新登录
        if (error.code == '401') {
          router.replace({
            path: 'login',
            query: {
                redirect: router.currentRoute.fullPath // 登录之后跳转到对应页面
            }
          })
        }
      }
    )
