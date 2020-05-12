---
  title: 关于vue项目中axios使用formdata的问题
  date: 2020-02-19
  tags:
      - vue
      - axios
  auth: xya370
  categories:
    - 踩坑日常
---

<!-- # 关于vue项目中axios使用formdata的问题 -->
  有时候，我们在使用axios时会使用formdata进行传参，但是此时axios是已经对get，post方法进行过封装了，这个时候要怎么办呢？

## 解决：
  在我们封装的http.js中加入一下一段代码：

    axios.defaults.headers.post['Content-Type'] = 'application/x-www-form-urlencoded'
    axios.defaults.headers.get['Content-Type'] = 'application/x-www-form-urlencoded'

    axios.defaults.transformRequest = [function (data) {
      let ret = ''
      for (let it in data) {
        ret += encodeURIComponent(it) + '=' + encodeURIComponent(data[it]) + '&'
      }
      return ret
    }]

  然后我们就可以通过formdata的形式进行传参了