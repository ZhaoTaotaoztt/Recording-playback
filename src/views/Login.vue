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
      <el-form-item label="* Username" prop="name">
        <el-input
          type="text"
          v-model="loginForm.username"
          placeholder="Username"
        ></el-input>
      </el-form-item>

      <el-form-item label="* Password" prop="pass">
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
        username: "",
        password: "",
      },
      loginRules: {
        username: [
          { required: true, trigger: "blur",message: "Please enter the account name!", validator: validateUsername },
        ],
        password: [
          { required: true, trigger: "blur", message: "The password length is 6 digits!",validator: validatePassword },
        ],
      },
      loading: false,
      passwordType: "password",
      redirect: undefined,
    };
  },
  methods: {
    handleLogin() {
      this.$refs.loginForm.validate((loginForm) => {
        // console.log(this.loginForm.username);
        if (loginForm) {
          if (
            this.loginForm.username == "admin" &&
            this.loginForm.password == 111111
          ) {
            console.log("管理员登录");
            // this.$store.commit("setUser", this.loginForm);
            // console.log(this.$store.state.user);

            this.$router.push("/home");

            //存数据
            window.localStorage.setItem("user", JSON.stringify(this.loginForm));
            console.log(JSON.parse(window.localStorage.getItem("user")));
          } else if (
            this.loginForm.username == "user" &&
            this.loginForm.password == 111111
          ) {
            console.log("用户登录");
            // this.$store.commit("setUser", this.loginForm);
            // console.log(this.$store.state.user);

            this.$router.push("/home");

            //存数据
            window.localStorage.setItem("user", JSON.stringify(this.loginForm));
            console.log(JSON.parse(window.localStorage.getItem("user")));
          } else {
            console.log("账号或者密码错误");
            this.$message({
              message: "Wrong account or password！", //账号或者密码错误
              type: "warning",
              duration: 0,
              showClose: true,
            });
          }

          setTimeout(() => {
            this.loginForm = {
              username: "",
              password: "",
            };
          }, 200);
        } else {
          console.log("error submit!!");
          setTimeout(() => {
            this.loginForm = {
              username: "",
              password: "",
            };
          }, 200);
          return false;
        }
      });
    },
  },
};
// export default class Home extends Vue {}
</script>

<style scoped>
body {
  width: 100%;
  height: 100%;
}
.home {
  /* background-color: pink; */
  position: relative;
  top: 50%;
  transform: translateY(50%);
}
.home >>> .el-form {
  width: 40%;
  height: 0px;
  /* background-color: pink; */
  margin: 150px auto;
}
.home >>> .el-form .el-form-item,
.home >>> .el-form .el-button {
  width: 90%;
}
.home >>> .el-form .el-button {
  width: 92% !important;
  background-color: rgb(245, 124, 0);
  border-color: rgb(245, 124, 0);
  color: black;
  font-size: 20px;
}
.home >>> .el-form #logo {
  width: 80%;
  height: 150px;
  background: url("../assets/logo1.png") no-repeat center;
  background-size: cover;
  margin: 40px auto;
  border: none;
}
@media screen and (min-width: 1500px) and (max-width: 2200px) {
  .home >>> .el-form #logo {
    width: 100%;
    height: 10rem;
    background: url("../assets/logo1.png") no-repeat center;
    background-size: 80%;
    margin: 40px auto;
  }
}
@media screen and (min-width: 200px) and (max-width: 1500px) {
  .home >>> .el-form {
    width: 80%;
    height: 0px;
    /* background-color: pink; */
    margin: 150px auto;
  }
  .home >>> .el-form #logo {
    width: 100%;
    height: 10rem;
    background: url("../assets/logo1.png") no-repeat center;
    background-size: 70%;
    margin: 40px auto;
  }
}
</style>