<template>
  <div class="site-wrapper site-page--login">
    <div class="site-content__wrapper">
      <div class="site-content">
        <div class="login-main">
          <h3 class="login-title">宠物登录</h3>
          <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmit()" status-icon>
            <el-form-item prop="userName">
              <el-input v-model="dataForm.userName" placeholder="帐号"></el-input>
            </el-form-item>
            <el-form-item prop="password">
              <el-input v-model="dataForm.password" type="password" placeholder="密码"></el-input>
            </el-form-item>
            <el-form-item prop="secondPassword">
              <el-input v-model="dataForm.secondPassword" type="password" placeholder="确认密码"></el-input>
            </el-form-item>
            <el-form-item>
              <el-button style="width: 100%" type="success" @click="dataFormSubmit()">注册</el-button>
            </el-form-item>
          </el-form>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
  import { getUUID } from '@/utils'
  export default {
    data () {
      return {
        dataForm: {
          userName: '',
          password: '',
          secondPassword: ''
        },
        dataRule: {
          userName: [
            { required: true, message: '帐号不能为空', trigger: 'blur' }
          ],
          password: [
            { required: true, message: '密码不能为空', trigger: 'blur' }
          ],
          secondPassword: [
            { required: true, message: '密码不能为空', trigger: 'blur' }
          ]
        },
        captchaPath: ''
      }
    },
    created () {
    },
    methods: {
      // 提交表单
      dataFormSubmit () {
        this.$refs['dataForm'].validate((valid) => {
          //判断两次密码是否相同
          if (this.dataForm.password !== this.dataForm.secondPassword){
            this.$message.warn("输入的两次密码不同!!!!!!")
            return;
          }
          if (valid) {
            this.$http({
              url: this.$http.adornUrl('/sys/user/save'),
              method: 'post',
              data: this.$http.adornData({
                'username': this.dataForm.userName,
                'password': this.dataForm.password
              })
            }).then(({data}) => {
              if (data && data.code === 0) {
                this.$message.success("注册成功")
                this.$router.replace({ name: 'login' })
              } else {
                this.$message.error(data.msg)
              }
            })
          }
        })
      }
    }
  }
</script>

<style lang="scss">
  .site-wrapper.site-page--login {
    position: absolute;
    top: 0;
    right: 0;
    bottom: 0;
    left: 0;
    background-color: rgba(38, 50, 56, .6);
    overflow: hidden;
  &:before {
     position: fixed;
     top: 0;
     left: 0;
     z-index: -1;
     width: 100%;
     height: 100%;
     content: "";
     background-image: url(~@/assets/img/login_bg.jpg);
     background-size: cover;
   }
  .site-content__wrapper {
    position: absolute;
    top: 0;
    right: 0;
    bottom: 0;
    left: 0;
    padding: 0;
    margin: 0;
    overflow-x: hidden;
    overflow-y: auto;
    background-color: transparent;
  }
  .site-content {
    min-height: 100%;
    padding: 30px 500px 30px 30px;
  }
  .brand-info {
    margin: 220px 100px 0 90px;
    color: #fff;
  }
  .brand-info__text {
    margin:  0 0 22px 0;
    font-size: 48px;
    font-weight: 400;
    text-transform : uppercase;
  }
  .brand-info__intro {
    margin: 10px 0;
    font-size: 16px;
    line-height: 1.58;
    opacity: .6;
  }
  .login-main {
    position: absolute;
    top: 0;
    right: 0;
    padding: 150px 60px 180px;
    width: 470px;
    min-height: 100%;
    background-color: #fff;
  }
  .login-title {
    font-size: 16px;
  }
  .login-captcha {
    overflow: hidden;
  > img {
    width: 100%;
    cursor: pointer;
  }
  }
  .login-btn-submit {
    width: 100%;
    margin-top: 38px;
  }
  }
</style>
