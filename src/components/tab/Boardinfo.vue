<!--
 * @Author: your name
 * @Date: 2022-02-08 11:15:01
 * @LastEditTime: 2022-02-21 14:14:12
 * @LastEditors: Please set LastEditors
 * @Description: 打开koroFileHeader查看配置 进行设置: https://github.com/OBKoro1/koro1FileHeader/wiki/%E9%85%8D%E7%BD%AE
 * @FilePath: \admin\src\components\tab\Boardinfo.vue
-->
<template>
  <div class="boardinfo">
    <div class="sn">
      <P>SN:PR882533_20210102</P>
      <P>BoardInfo</P>
    </div>

    <table>
      <tr>
        <th>ChannelIndex</th>
        <th>ChannelStatus</th>
        <th>BoardSN</th>
        <th>BoardVersion</th>
        <th>temperature</th>
      </tr>
      <tr v-for="item in boardinfo" :key="item.id">
        <td>{{ item.ChannelInformations.ChannelIndex }}</td>
        <td>{{ item.ChannelInformations.ChannelStatus }}</td>
        <td>{{ item.ChannelInformations.BoardSN }}</td>
        <td>{{ item.ChannelInformations.BoardVersion }}</td>
        <td>{{ item.ChannelInformations.temperature }}</td>
      </tr>
    </table>
    <p>
      <el-button id="btn" class="btn" @click="getBoardinfo"
        >Query Board Info</el-button
      >
    </p>
    <p>
      <el-button type="text" id="btn" class="btn" divided @click="Signout"
        >SignOut</el-button
      >
    </p>
    <div class="showWarn" v-show="showWarn">请不要频繁点击！</div>
  </div>
</template>

<script>
// import $ from "jquery";
// import "jquery-ui-dist/jquery-ui";
// import "jquery-ui-dist/jquery-ui.min.css";

export default {
  data() {
    return {
      boardinfo: [],
      navactive: false,
      isclick: true,
      showWarn: false,
    };
  },
  mounted() {
  },
  methods: {
    getBoardinfo() {
      //获取boardinfo数据
      //socket请求
      // let ws = new WebSocket("ws://139.198.123.178:2000");
      // ws.onopen = function () {
      //   console.log("连接成功");
      //   ws.send({
      //     APIName: "QueryPlayId",
      //     FileInformations: {
      //       FileName:
      //         "gnss_3345678683688856_20211227_103250_325_115910_456.bin",
      //       PlayBoardIndexs: [],
      //     },
      //   });
      //   console.log("给服务端发送一个字符串：数据");
      // };
      // ws.onmessage = function (e) {
      //   console.log("收到服务端的消息：" + e.data);
      // };

      //模拟请求数据
      this.axios.get("/QueryBoardInfo").then((res) => {
        this.boardinfo = res.data;
      });
      
      //提示不要频繁点击
      if (this.isclick) {
        this.isclick = false;
        setTimeout(() => {
          this.isclick = true;
        }, 4000);
      } else {
        this.$message({
          message: "请不要频繁点击！",
          type: "warning",
        });
      }
      //提示不要频繁点击

    },
    Signout() {
      //退出登录
      this.$confirm("确认关闭？")
        .then((_) => {
          this.$store.commit("setUser", null);
          this.$router.push("/");
        })
        .catch((err) => {
          console.log(err);
        });
    },
    clickHandle() {
      console.log(aaa);
    },
  },
};
</script>

<style scoped>
.sn {
  font-size: 1.5rem;
  text-indent: 4rem;
}
table {
  width: 90%;
  height: 10rem;
  /* background-color: pink; */
  margin: 0rem auto;
}
table th {
  height: 5rem;
  font-size: 1.3rem;
  border-bottom: 1px solid gainsboro;
}
table td {
  height: 5rem;
  /* background-color: aqua; */
  text-align: center;
  border-bottom: 1px solid gainsboro;
  color: rgb(151, 151, 146);
}
.boardinfo {
  position: relative;
}
.boardinfo >>> p .el-button {
  background-color: rgb(245, 154, 35);
  color: white;
  font-size: 1rem;
  margin-left: 50px;
  width: 250px;
}
.boardinfo > .showWarn {
  width: 20%;
  height: 4rem;
  background-color: rgb(253, 246, 236);
  position: absolute;
  z-index: 100;
  top: -2%;
  left: 50%;
  transform: translateX(-50%);
  letter-spacing: 0.3rem;
  font-size: 1.4rem;
  text-align: center;
  line-height: 4rem;
  color: rgb(245, 124, 0);
  border-radius: 5px;
  border: rgb(245, 124, 0) solid 1px;
}
</style>
