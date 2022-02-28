<template>
  <div class="home">
    <el-form
      ref="loginForm"
      :model="loginForm"
      :rules="loginRules"
      label-width="100px"
      class="demo-ruleForm"
    >
      <div id="logo"></div>
      <el-form-item label="*Username" prop="name">
        <el-input
          type="text"
          v-model="loginForm.username"
          placeholder="Username"
        ></el-input>
      </el-form-item>

      <el-form-item label="*Password" prop="pass">
        <el-input
          type="password"
          autocomplete="on"
          v-model="loginForm.password"
          placeholder="Password"
        ></el-input>
      </el-form-item>

      <el-button
        id="submit"
        type="primary"
        class="sbumit"
        style="width: 100%; margin-bottom: 30px"
        @click.native.prevent="handleLogin"
        >SIGN IN</el-button
      >
    </el-form>
  </div>
</template>

<script >
// import { Component, Vue } from 'vue-property-decorator';
// import HelloWorld from '@/components/HelloWorld.vue'; // @ is an alias to /src
import { validUsername } from "@/utils/validate";
// import ws from "../utils/websocket"

export default {
  name: "login",
  data() {
    const validateUsername = (rule, value, callback) => {
      if (!validUsername(value)) {
        callback(new Error("Please enter the correct user name"));
      } else {
        callback();
      }
    };
    const validatePassword = (rule, value, callback) => {
      if (value.length < 6) {
        callback(new Error("The password can not be less than 6 digits"));
      } else {
        callback();
      }
    };
    return {
      loginForm: {
        username: "admin",
        password: "111111",
      },
      loginRules: {
        username: [
          { required: true, trigger: "blur", validator: validateUsername },
        ],
        password: [
          { required: true, trigger: "blur", validator: validatePassword },
        ],
      },
      loading: false,
      passwordType: "password",
      redirect: undefined,
    };
  },
  methods: {
    handleLogin() {
      // ws.initWebSocket("ws://192.168.1.203:9001")

      this.$refs.loginForm.validate((valid) => {
        if (valid) {
          this.$router.push("/home");
          this.$store.commit("setUser", this.loginForm);
        } else {
          console.log("error submit!!");
          return false;
        }
      });

    },
  },
};
// export default class Home extends Vue {}
</script>

<style scoped>
.home >>> .el-form {
  width: 40%;
  height: 300px;
  /* background-color: pink; */
  margin: 150px auto;
}
.home >>> .el-form .el-form-item,
.home >>> .el-form .el-button {
  width: 90%;
}
.home >>> .el-form .el-button {
  background-color: rgb(245, 124, 0);
  border-color: rgb(245, 124, 0);
  color: black;
  font-size: 20px;
}
.home >>> .el-form #logo {
  width: 80%;
  height: 100px;
  background: url("../assets/logo.png") no-repeat center;
  background-size: 90%;
  margin: 40px auto;
  border: none;
}

@media screen and (min-width: 200px) and (max-width: 800px) {
  .home >>> .el-form {
    width: 80%;
    height: 300px;
    /* background-color: pink; */
    margin: 150px auto;
  }
  .home >>> .el-form #logo {
    width: 100%;
    height: 10rem;
    background:url("../assets/logo.png") no-repeat center;
    background-size: 90%;
    margin: 40px auto;
    border: 1px solid black;
  }
}
</style>