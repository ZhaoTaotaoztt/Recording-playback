
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
      <tr v-for="item in boardinfo" :key="item.index" @click="test(item)">
        <td>{{ item.ChannelIndex }}</td>
        <td>{{ item.ChannelStatus }}</td>
        <td>{{ item.BoardSN }}</td>
        <td>{{ item.BoardVersion }}</td>
        <td>{{ item.temperature }}</td>
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
// import ws from "../utils/websocket"
// import {
// websocketOpen,sendSock,websocketonmessage,websocketclose
// } from "../../utils/websocket";
export default {
  data() {
    return {
      boardinfo: [],
      navactive: false,
      isclick: true, //用来判断提示不要频繁点击的布尔值
      showWarn: false,
    };
  },
  mounted() {},
  methods: {
    test(item) {
      console.log(item);
    },
    getBoardinfo() {
      //获取boardinfo数据
      //socket请求----
      var ws = new WebSocket("ws://192.168.1.203:9001");
      ws.onopen = function (e) {
        // console.log(ws.readyState);
        ws.send(
          JSON.stringify({
            cmd: {
              APIName: "QueryBoardInfo",
              SN: "NA",
              ChannelNumber: -1,
              ChannelInformations: [
                {
                  ChannelIndex: 0,
                  ChannelStatus: "NA",
                  BoardSN: "NA",
                  BoardVersion: "NA",
                  temperature: "0",
                },
              ],
            },
          })
        );
      };
      var that = this;
      ws.onmessage = function (e) {
        console.log(JSON.parse(e.data).cmd.ChannelInformations);
        var obj = JSON.parse(e.data).cmd.ChannelInformations;
        that.boardinfo = obj;

        //关闭TCP连接
        ws.close();
        ws.onclose = function (e) {
          console.log(e);
        };
      };

      //socket请求----

      //模拟请求数据
      // this.axios.get("/QueryBoardInfo").then((res) => {
      //   this.boardinfo = res.data;
      // });

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
