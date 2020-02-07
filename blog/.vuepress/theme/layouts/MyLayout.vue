<template>
  <div class="Layout_main">
    <div class="Layout_main-left">
      <div class="Layout_main-title">
        <NavLink link="/" class="home-link">{{ $site.title }} </NavLink>
      </div>
      <div class="Layout_main-info">
        <div class="info-auto">
          <div class="info-images">
          </div>
        </div>
        <span class="info-description">{{ $site.description || '暂时还不想写哦'}}</span>

        <div class = "Layout_main-blogsTag">
          <div class="blogs-classfiction">文章分类</div>
          <ul>
            <li v-for = "(item, index) of tags" class="li-tags">
              <NavLink :link="item.path" class="tag-link">{{ item.name }} </NavLink>
              <span>({{item.pages.length}})</span>
            </li>
          </ul>
        </div>
      </div>
    </div>
    <div class="Layout_main-right">
      <div class="Layout_main-top">
        <div style="position:relative;width: 100%;height:100%">
          <SearchBox />

          <router-link to='/'>
            <div class="Layout-home">
              <img src="../images/home.png" alt="">
            </div>
          </router-link>
        </div>
      </div>
      <div class="Layout_main-blogs">
        <div class="content-wrapper">
          <DefaultGlobalLayout />
        </div>
      </div>
    </div>
  </div>
</template>

<script>
  import SearchBox from '@SearchBox'
  import GlobalLayout from '@app/components/GlobalLayout.vue'
  export default {
    name: "MyLayout",
    components: {
      SearchBox,
      DefaultGlobalLayout: GlobalLayout,
    },
    data(){
      return {
        tags: this.$root.$classification.list || []
      }
    },
    beforeRouteLeave(to, from, next){
      console.log(to, from, next)
      next()
    }
  }
</script>

<style lang="stylus">
@import '~@app/style/config'
  .Layout_main
    width: 100%
    height: 100%
    display: flex

  .Layout_main-left
    flex: 1
    padding: 10px
    position: fixed
    top: 0
    left: 0
    width: 20%
    bottom: 61px


  .Layout_main-right
    flex: 4
    padding: 10px
    margin-left: 20%
    padding: 10px
    padding-bottom: 61px

  .Layout_main-top
    height: fit-content
    padding: 10px
    top: 0
    background: #fff
    z-index: 2
    width: 80%
    position: fixed

  .search-box
    width: 25%
    border-radius: 15px
    padding-left: 20px

  .search-box input
    width: 100%
    border: 1px solid #c0c4cc

  .Layout_main-title
    padding-left: 20px
    font-size: 28px
    font-weight: 400
    color: #1f2f3d
  .Layout_main-title  .nav-link:hover, .Layout_main-title .nav-link.router-link-active
    color #1f2f3d

  .Layout_main-blogs
    margin-top: 40px

  .info-images
    width 10rem
    height 10rem
    background url(../images/my.png) no-repeat
    background-size: contain;
    border-radius: 50%;
    margin auto
    position: relative

  .info-images:after
    position absolute
    width 10.3rem
    height 10.3rem
    content  ""
    background url(../images/my.png) no-repeat
    background-size: contain;
    border-radius: 50%;
    z-index -1
    filter blur(10px)

  .Layout_main-info
    padding-top 20px
    height calc(100% - 37px)
    border-right 1px solid #eee

  .info-auto
    position relative

  .info-description
    width 100%
    padding 10px
    font-size 12px
    display inline-block
    text-align center

  .Layout_main-blogsTag
    padding 12px 14px
    width 70%
    margin 20px auto 0
    box-shadow 0 4px 9px #ddd
    background #fff

  .blogs-classfiction
    font-size: 20px
    font-weight: 400
    color: #1f2f3d

  .li-tags
    display flex

  .tag-link
   flex 1 1

  .Layout-home
    position absolute
    right 66px
    top 0
    padding-bottom 10px
</style>