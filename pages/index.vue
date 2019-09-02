<template>
  <div class="container">
    <!-- 轮播图组件:el-carousel -->
    <!-- arrow: 左右切换的箭头总是显示的 -->
    <el-carousel
    arrow="always">
      <el-carousel-item v-for="(item, index) in banners" :key="index">
        <div class="banner-image"
        :style="`
        background:url(${$axios.defaults.baseURL + item.url}) center center no-repeat;
        background-size:contain contain;
        `"></div>
      </el-carousel-item>
    </el-carousel>
  </div>
</template>

<script>
export default {
  data () {
    return {
      // 轮播图数组
      banners:[
        "http://157.122.54.189:9095/assets/images/th01.jfif",
        "http://157.122.54.189:9095/assets/images/th02.jfif",
        "http://157.122.54.189:9095/assets/images/th03.jfif"
      ]
    }
  },
  mounted(){
    // 请求轮播图的数据
    this.$axios({
      url: "/scenics/banners"
    }).then(res=>{
      // 获取轮播图的数组
      const data = res.data.data
      // 赋值给banners
      this.banners = data
    })
    console.dir(this.$axios)
  }
}
</script>

<style lang="less" scoped>
.container{
    min-width:1000px;
    margin:0 auto;
    position:relative;

    //  如果再scoped中要修改第三方的组件，
    // 组件的class不会加上scoped的字符串，需要在前面加个/deep/
    /deep/ .el-carousel__container{
        height:700px;
    }

    .banner-image{
    width:100%;
    height:100%;
    }
}
</style>