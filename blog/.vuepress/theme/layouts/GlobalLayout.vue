<template>
  <div id="vuperess-theme-blog__global-layout">
    <div class="blog-theme_main">
         <!-- <Header />
         <Classfication/>
               <div class="content-wrapper" @click="isMobileHeaderOpen = false">
                 <DefaultGlobalLayout />
               </div>
                <MobileHeader
         :is-open="isMobileHeaderOpen"
         @toggle-sidebar="isMobileHeaderOpen = !isMobileHeaderOpen"
                /> -->
        <MyLayout/>
        <!-- <MobileLayout v-show="isMobile()"/> -->
    </div>
    <Footer />
  </div>
</template>

<script>
import GlobalLayout from '@app/components/GlobalLayout.vue'
import Header from '@theme/components/Header.vue'
import MobileHeader from '@theme/components/MobileHeader.vue'
import Footer from '@theme/components/Footer.vue'
import MyLayout from "./MyLayout.vue"
// import Classfication from '../components/Classfiction.vue'
import MobileLayout from "./MobileLayout";
export default {
  components: {
    DefaultGlobalLayout: GlobalLayout,
    Header,
    MobileHeader,
    Footer,
    MyLayout,
    // Classfication,
    MobileLayout
  },

  data() {
    return {
      isMobileHeaderOpen: false,
    }
  },

  mounted() {
    console.log(this.isMobile())
    this.$router.afterEach(() => {
      this.isMobileHeaderOpen = false
    })
  },
  methods: {
    isMobile(){
      // return /Android|webOS|iPhone|iPod|BlackBerry/i.test(navigator.userAgent)
    }
  }
}
</script>

<style lang="stylus">
.blog-theme_main
  width: 100%
  height: 100%
  display: flex

.blog-theme_main-left
  flex: 1
  position: relative
.blog-theme_main-right
  flex: 2

.blog-theme_main-classfition
  position: absolute
  top:23%
  right: 10%
  width: 200px
  height: 100px

.content-wrapper
  padding 10px 15px 80px 15px
  min-height calc(100vh - 80px - 60px - 160px)
  max-width 740px
  //margin 0 auto

@media (max-width: $MQMobile)
  .content-wrapper
    padding 100px 15px 20px 15px
    min-height calc(100vh - 20px - 60px - 100px)
</style>
