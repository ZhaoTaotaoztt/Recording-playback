
<template>
  <div class="boardinfo">
    <div class="sn">
      <!-- <P
        >IP:&nbsp;<input
          type="text"
          name=""
          id="saveid"
          v-model="ws"
          @change="SaveIP"
      /></P> -->
      <P>SN:&nbsp;{{ SN }}</P>
      <P v-show="isShowTable">BoardInfo</P>
    </div>

    <table v-show="isShowTable">
      <tr>
        <th>Port</th>
        <th>PortStatus</th>
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
      <el-button
        v-show="isShowTable"
        id="btn"
        class="btn"
        @click="getBoardinfo"
        v-preventReClick="5000"
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
export default {
  data() {
    return {
      SN: "",
      boardinfo: [],
      navactive: false,
      isclick: true, //用来判断提示不要频繁点击的布尔值
      showWarn: false,
      showBoard: false, //是否有权限查看信息
      ws: "192.168.1.75", //ip地址
      isShowTable: true, //是否显示table信息
    };
  },
  mounted() {
    //获取域名
    console.log("hostname", window.location.hostname);
    var ws = window.location.hostname;
    if ((ws = "localhost")) {
      ws = "192.168.1.75";
      this.$store.commit("getIP", ws);
      console.log(this.$store.state.ws);
    } else {
      this.$store.commit("getIP", ws);
      console.log(this.$store.state.ws);
    }

    //socket请求
    var ws = new WebSocket("ws://" + this.ws + ":9001");
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
      that.SN = JSON.parse(e.data).cmd.SN;
      console.log(JSON.parse(e.data).cmd);

      //关闭TCP连接
      ws.close();
      ws.onclose = function (e) {
        // console.log(e);
      };
    };
    //socket请求

    var user = JSON.parse(window.localStorage.getItem("user"));
    console.log(user.username);
    if (user.password == "changeself111111") {
      this.showBoard = true;
      this.isShowTable = true;
    } else {
      this.isShowTable = false;
      return;
    }
  },
  methods: {
    //查询板卡信息
    getBoardinfo() {
      if (this.showBoard !== true) {
        // this.showBoard = false;
        console.log("您未有权限查看");
        this.showBoard = false;
        this.$message({
          message: "You do not have permission to view！", //您未有权限查看
          type: "warning",
          duration: 0,
          showClose: true,
        });
      } else {
        console.log("显示成功");
        //获取boardinfo数据
        //socket请求----
        var ws = new WebSocket("ws://" + this.ws + ":9001");
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
          var obj = JSON.parse(e.data).cmd.ChannelInformations;
          that.boardinfo = obj;

          var arr = JSON.parse(e.data).cmd.ChannelInformations;
          var data = arr.filter(function (item) {
            return item.ChannelStatus == "idle";
          });
          var ChannelIndex = [];
          for (var i = 0; i < data.length; i++) {
            ChannelIndex.unshift({ ChannelIndex: data[i].ChannelIndex });
          }

          console.log(arr);
          console.log(data);
          console.log(ChannelIndex);
          that.$store.commit("getChannelIndex", ChannelIndex);

          //关闭TCP连接
          ws.close();
          ws.onclose = function (e) {
            console.log(e);
          };
        };
        //socket请求----
      }
    },

    //退出登录
    Signout() {
      this.$confirm("Are you sure to close it？", {
        confirmButtonText: "ACCEPT",
        cancelButtonText: "CANCEL",
      })
        .then((_) => {
          window.localStorage.removeItem("user");
          this.$router.push("/");
        })
        .catch((err) => {
          console.log(err);
        });
    },

    // //存储请求的IP地址
    // SaveIP() {
    //   console.log(this.ws);
    //   var ws = this.ws;
    //   this.$store.commit("getIP", ws);
    //   console.log(this.$store.state.ws);
    // },
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
  background-color: rgb(37,91,150);
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
#saveid {
  outline-color: rgb(35, 141, 222);
  border: 1px solid rgb(231, 236, 247);
  width: 15%;
  height: 20%;
  border-radius: 5px;
}
</style>
