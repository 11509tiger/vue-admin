<template>
  <div id="login">
    <div class="login-wrap">
      <ul class="menu-tab">
        <li v-for="item in menuTab" :key="item.id" :class="{'current':item.current}" @click="toggleMenu(item)">{{item.tab}}</li>
      </ul>
      <!-- 表单start -->
      <el-form :model="ruleForm" status-icon :rules="rules" ref="ruleForm" class="login-form" size="medium">
        <el-form-item prop="username" class="item-form">
          <label>邮箱</label>
          <el-input type="text" v-model="ruleForm.username" autocomplete="off"></el-input>
        </el-form-item>

        <el-form-item prop="password" class="item-form">
          <label>密码</label>
          <el-input type="text" v-model="ruleForm.password" autocomplete="off" minlength="6" maxlength="20"></el-input>
        </el-form-item>

        <el-form-item prop="passwords" class="item-form" v-show="menuTab[1].current">
          <label>重复密码</label>
          <el-input type="text" v-model="ruleForm.passwords" autocomplete="off" minlength="6" maxlength="20"></el-input>
        </el-form-item>

        <el-form-item prop="code" class="item-form">
          <label>验证码</label>
          <el-row :gutter="11">
            <el-col :span="15">
              <el-input v-model="ruleForm.code" minlength="6" maxlength="6"></el-input>
            </el-col>
            <el-col :span="9">
              <el-button type="success">获取验证码</el-button>
            </el-col>
          </el-row>
        </el-form-item>
        
        <el-form-item>
          <el-button type="danger" @click="submitForm('ruleForm')" class="login-btn block">提交</el-button>
        </el-form-item>
      </el-form>
    </div>
  </div>
</template>

<script>
import {stripscript, validateEmail, validatePass, validateVCode} from '@/utils/validate';
export default {
  name:'login',
  data(){
    // 验证用户名
    let validateUsername = (rule, value, callback) => {
      if (value === '') {
        callback(new Error('请输入用户名'));
      } else if(validateEmail(value)){
        callback(new Error('用户名格式不正确'));
      } else {
        callback();
      }
    };
    // 验证密码
    var validatePassword = (rule, value, callback) => {
      // 过滤后的数据
      this.ruleForm.password=stripscript(value);
      value = this.ruleForm.password;
      if (value === '') {
        callback(new Error('请输入密码'));
      } else if (validatePass(value)) {
        callback(new Error('密码为6-20位数字+字母'));
      } else {
        callback();
      }
    };
    // 验证重复密码
    var validatePasswords = (rule, value, callback) => {
      // 判断菜单按钮的current值, 为true直接通过
      if(this.menuTab[0].current === true) { callback(); }
      this.ruleForm.passwords=stripscript(value);
      value = this.ruleForm.passwords;
      if (value === '') {
        callback(new Error('请再次输入密码'));
      } else if (value != this.ruleForm.password) {
        callback(new Error('重复密码不一致'));
      } else {
        callback();
      }
    };
    // 验证验证码
    var validateCode = (rule, value, callback) => {
      this.ruleForm.code=stripscript(value);
      value = this.ruleForm.code;
      if (!value) {
        return callback(new Error('请输入验证码'));
      } else if (validateVCode(value)) {
        callback(new Error('请输入6位验证码'));
      } else {
        callback();
      }
    };
    return {
      menuTab:[
        {tab:"登录", current:true},
        {tab:"注册", current:false}
      ],
      // 表单的数据
      ruleForm: {
        username: '',
        password: '',
        passwords:'',
        code: ''
      },
      rules: {
        username: [
          { validator: validateUsername, trigger: 'blur' }
        ],
        password: [
          { validator: validatePassword, trigger: 'blur' }
        ],
        passwords: [
          { validator: validatePasswords, trigger: 'blur' }
        ],
        code: [
          { validator: validateCode, trigger: 'blur' }
        ]
      }
    }
  },
  created() {
    
  },
  mounted() {
    
  },
  methods: {
    toggleMenu(data){
      this.menuTab.forEach((elem,index)=>{
        elem.current = false;
      });
      // 高光
      data.current=true
    },
    submitForm(formName) {
      this.$refs[formName].validate((valid) => {
        if (valid) {
          alert('submit!');
        } else {
          console.log('error submit!!');
          return false;
        }
      });
    }
  }
}
</script>

<style lang="scss" scoped>
#login{
  height: 100vh;
  background-color: #344a5f;
  display: flex;
}
.login-wrap{
  width: 330px;
  margin: 5vh auto;
}
.menu-tab{
  text-align: center;
  li{
    display: inline-block;
    width: 88px;
    line-height: 36px;
    font-size: 14px;
    color: #fff;
    border-radius: 2px;
    cursor: pointer;
  }
  .current{
    background-color: rgba(0, 0, 0, .1);
  }
}
.login-form{
  margin-top: 29px;
  label{
    display: block;
    margin-bottom: 3px;
    color: #fff;
    font-size: 14px;
  }
  .item-form{ margin-bottom: 13px; }
  .block{
    display: block;
    width: 100%;
  }
  .login-btn{ margin-top: 19px; }
}
</style>