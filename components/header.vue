<template>
  <div class="header">
    <el-row type="flex" justify="space-between" class="main">
      <!-- logo -->
      <div class="logo">
        <nuxt-link to="/">
          <img src="http://157.122.54.189:9093/images/logo.jpg" alt />
        </nuxt-link>
      </div>

      <!-- 菜单栏 -->
      <el-row type="flex" class="navs">
        <nuxt-link to="/">首页</nuxt-link>
        <nuxt-link to="/post">旅游攻略</nuxt-link>
        <nuxt-link to="/hotel">酒店</nuxt-link>
        <nuxt-link to="/air">国内机票</nuxt-link>
      </el-row>

      <!-- 登陆/用户信息 -->
      <!-- <div v-if="!$store.state.user.userInfo.token"> -->
      <div v-if="!$store.state.user.userInfo.token">
        <nuxt-link to="/user/login">登录 / 注册</nuxt-link>
      </div>

      <div v-else>
        <!-- {{$store.state.user.userInfo.user.nickname}} -->
        <el-dropdown>
         <span class="el-dropdown-link">
           <!-- 头像,昵称 -->
           <img :src="` ${$axios.defaults.baseURL}${$store.state.user.userInfo.user.defaultAvatar} `">
           <span>{{$store.state.user.userInfo.user.nickname}}</span>
           <i class="el-icon-arrow-down el-icon--right"></i>
         </span>
         <el-dropdown-menu slot="dropdown">
           <el-dropdown-item>个人中心</el-dropdown-item>
            <!-- click.native 给第三方组件添加事件需要加上native -->
           <el-dropdown-item @click.native="handleLogout">退出</el-dropdown-item>
         </el-dropdown-menu>
        </el-dropdown>
      </div>
    </el-row>
  </div>
</template>

<script>
export default {
  // 组件加载
	mounted(){
    console.log(this.$store.state.user.userInfo.token)
  },
  methods: {
    // 退出
    handleLogout(){
      // 清除登陆信息
      this.$store.commit('user/clearUserInfo')

      this.$message({
        type: 'success',
        $message: '退出成功'
      })
    }
  }
}
</script>

<style lang="less" scoped>
// 头像样式
.el-dropdown-link img{
    width: 36px;
    height:36px;
    border-radius: 50%;
    vertical-align: middle;
    box-sizing: border-box;
    border:2px #fff solid;
    &:hover{
        border:2px #409eff solid;
    }
}

.header {
  height: 60px;
  line-height: 60px;
  width: 100%;
  background-color: #fff;
  border-bottom: 1px solid #ddd;
  box-shadow: 0 2px 2px #ddd;
  box-sizing: border-box;

  .main {
    width: 1000px;
    margin: 0 auto;
  }

  .logo {
    width: 156px;
    padding-top: 8px;

    img {
      display: block;
      width: 100%;
    }
  }

  .navs {
    margin: 0 20px;
    flex: 1;

    a {
      display: block;
      padding: 0 20px;
      height: 60px;
      box-sizing: border-box;

      &:hover{
      // &:focus,
      // &active 
        border-bottom: 5px #409eff solid;
        color: #409eff;
      }
    }

    /deep/.nuxt-link-exact-active {
      background: #409eff;
      color: #fff;

      &:hover {
        color: #fff;
      }
    }
  }
}
</style>