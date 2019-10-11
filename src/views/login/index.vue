<template>
  <div class="login">
    <div class="login-warp">
      <div class="loginlogo">
        <img src="../../assets/logo_index.png" alt />
      </div>
      <el-form ref="form" :model="form" :rules="rules">
        <el-form-item prop="mobile">
          <el-input v-model="form.mobile" placeholder="请输入电话号码"></el-input>
        </el-form-item>
        <el-form-item prop="code">
          <el-row>
            <el-col :span="14">
              <el-input v-model="form.code" placeholder="请输入验证码"></el-input>
            </el-col>
            <el-col :offset="4" :span="2">
              <el-button class="!!timer" @click="getCode">{{timer?`${codetime}s后获取`:'获取验证码'}}获取验证码</el-button>
            </el-col>
          </el-row>
        </el-form-item>
        <el-form-item prop="read">
          <el-checkbox v-model="form.read" name="type">
            我已阅读并同意
            <a href="#">用户协议</a>和
            <a href="#">隐私条款</a>
          </el-checkbox>
        </el-form-item>
        <el-form-item>
          <el-button class="btn" :loading="loginloading" type="primary" @click="login">登录</el-button>
        </el-form-item>
      </el-form>
    </div>
  </div>
</template>

<script>
import axios from 'axios'
export default {
  data () {
    return {
      form: {
        mobile: '13911111111',
        code: '246810',
        read: false
      },
      loginloading: false,
      rules: {
        mobile: [
          { required: true, message: '请输入电话号码', trigger: 'blur' },
          { min: 11, max: 11, message: '长度为11个字符', trigger: 'blur' }
        ],
        code: [
          { required: true, message: '请输入活动名称', trigger: 'blur' },
          { min: 6, max: 6, message: '长度为6个字符', trigger: 'blur' }
        ],
        read: [
          { required: true, message: '请先阅读条款', trigger: 'change' },
          { pattern: /true/, message: '请先阅读条款', trigger: 'change' }
        ]
      },
      codetime: 5,
      timer: null
    }
  },
  methods: {
    login () {
      this.$refs['form'].validate(valid => {
        if (valid) {
          this.submitData()
        } else {
        }
      })
    },
    submitData () {
      this.loginloading = true
      axios({
        url: 'http://ttapi.research.itcast.cn/mp/v1_0/authorizations',
        method: 'post',
        data: this.form
      })
        .then(res => {
          this.$router.push('/') // 路由重定项目
          this.$message({
            message: '恭喜你，这是一条成功消息',
            type: 'success'
          })
          this.loginloading = false
        })
        .catch(err => {
          console.log(err)
          this.$message.error('错了哦，这是一条错误消息')
        })
    },
    getCode () {
      this.$refs['form'].validateField('mobile', errMsg => {
        if (errMsg.trim().length > 0) {

        } else {
          this.timer = setInterval(() => {
            this.codetime--
            if (this.codetime <= 0) {
              clearTimeout(this.timer)
              this.codetime = 5
              this.timer = null
            }
          }, 1000)
        }
      })
    }
  }
}
</script>

<style lang="less" scoped>
.login {
  width: 100%;
  height: 100%;
  display: flex;
  justify-content: center;
  align-items: center;
  background: #ccc;
  .login-warp {
    background: #fff;
    padding: 35px;
    min-width: 300px;
    .btn {
      width: 100%;
    }
    .loginlogo {
      text-align: center;
      img {
        width: 150px;
        margin-bottom: 20px;
      }
    }
  }
}
</style>
