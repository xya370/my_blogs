<template>
  <div class="mobileLayout">
    <div class="mobileLayout-header"></div>
    <div class = "mobileLayout-body">
      <DefaultGlobalLayout />
      <!-- <div class = "mobileLayout-imgs">
        <img src="../images/my.png" alt="" class = "myPng">
      </div>
      <div class = "mobileLayout-classfictions">
        <div class = "classifiction_item" v-for = "(item, index) of classification">
           <NavLink :link="item.path" class="tag-link">{{ item.name }} </NavLink>
        </div>
      </div> -->
    </div>
  </div>
</template>

<script>
  import GlobalLayout from '@app/components/GlobalLayout.vue'
  export default {
    name: 'mobileLayout',
    data(){
      return {
        classification: this.$root.$classification.list || []
      }
    },
    components: {
      DefaultGlobalLayout: GlobalLayout,
    },
  }
</script>

<style lang = "stylus">
  .mobileLayout
    width 100%
    height 100%

  .mobileLayout-header
    height 3.75rem
    border-bottom 1px solid #eee

  .mobileLayout-body
    position relative
    height calc( 100% - 3.75rem)

  .mobileLayout-imgs
    height 120px
    text-align center
    padding-top 20%

  .myPng
    height 100%
    border-radius 50%

  .mobileLayout-classfictions
    position relative
    padding-top 30px
    display flex
    justify-content center

  .classifiction_item
    font-size 14px
    display inline-block
    padding 4px 8px
</style>
