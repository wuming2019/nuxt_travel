<template>
  <el-form :model="form" :rules="rules" ref="form" class="form">
    <el-form-item class="form-item" prop="username">
      <el-input placeholder="用户名手机" v-model="form.username"></el-input>
    </el-form-item>

    <el-form-item class="form-item" prop="captcha">
      <el-input placeholder="验证码" v-model="form.captcha">
        <template slot="append">
          <el-button @click="handleSendCaptcha">发送验证码</el-button>
        </template>
      </el-input>
    </el-form-item>

    <el-form-item class="form-item" prop="nickname">
      <el-input placeholder="你的名字" v-model="form.nickname"></el-input>
    </el-form-item>

    <el-form-item class="form-item" prop="password">
      <el-input placeholder="密码" type="password" v-model="form.password"></el-input>
    </el-form-item>

    <el-form-item class="form-item" prop="checkPassword">
      <el-input placeholder="确认密码" type="password" v-model="form.checkPassword"></el-input>
    </el-form-item>

    <el-button class="submit" type="primary" @click="handleRegSubmit">注册</el-button>
  </el-form>
</template>

<script>
export default {
    data () {
        // rule当前的规则是空的
        // value输入框的值
        // callback是回调函数，必须要调用
        const checkPassword = (rule,value,callback) => {
            if(!value){
                callback(new Error('请再次输入密码'))
            } else if(value !== this.form.password){
                callback(new Error('两次输入密码不一致!'))
            } else {
                // 代表验证通过
                callback()
            }
        }
        return {
            // 表单数据
            form:{
                username: "", // 用户名
                nickname: "", // 昵称
                captcha: "", // 验证码
                password: "", // 密码
                checkPassword: "", // 确认密码 
            },
            // 表单规则
            rules:{
                username: [{required: true, message: '用户名不能为空', trigger: 'blur'}],
                nickname: [{required: true, message: '昵称不能为空', trigger: 'blur'}],
                captcha: [{required: true, message: '验证码不能为空', trigger: 'blur'}],
                password: [{required: true, message: '密码不能为空', trigger: 'blur'}],
                checkPassword: [{validator: checkPassword, trigger: 'blur'}]
            }
        }
    },
    methods: {
        // 发送验证码
        handleSendCaptcha(){
            console.log(this.form)
        }
    },
}
</script>

<style lang="less" scoped>
.form{
    padding: 25px;
}

.el-form-item{
    margin-bottom: 20px;
}

.form-text{
    font-size:12px;
    color:#409EFF;
    text-align: right;
    line-height: 1;
}

.submit{
    width: 100%;
    margin-top: 10px;
}
</style>