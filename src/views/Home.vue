
<template>
  <div class="home">
    <div class="hearder">
      <div class="left">Record and Replay Tool</div>
      <div class="right"></div>
    </div>

    <div class="nav">
      <router-link to="/home/boardinfo">Boardinfo</router-link>
      <router-link to="/home/record">Record</router-link>
      <router-link to="/home/replay">Replay</router-link>
    </div>

    <router-view />
  </div>
</template>

<script>



export default {
  data() {
    return {

      activeName: "first",
      boardinfo: [],
      record: [],
      replay: [],
      dialogVisible: false,
      dialogrecord: false,
      dialogreplay: false,

      recordid: [],
      recordboolen: false,

      replayid: [],
      replayboolen: false,

      recordform: {
        boardindex: 0,
        filesize: "1",
        bitnum: "2",
        samplerate: "3",
        recordbandwidth: "4",
        recordfrequency: "5",
        recordxrgain: "6",
      },

      formLabelWidth: "120px",
    };
  },
  created() {},
  methods: {

    
    //replay部分replay部分replay部分replay部分replay部分replay部分replay部分replay部分replay部分replay部分
    getReplay() {
      //获取replay数据
      this.axios.get("/replay").then((res) => {
        this.replay = res.data;
        // console.log(this.replay);
      });
    },

    Checkedreplay(id, istrue) {
      //复选框选中的事件
      // console.log(id, istrue);
      if (istrue == true) {
        this.replayid = id;
        this.replayboolen = istrue;
      } else {
        this.replayboolen = istrue;
      }
    },
    Deletreplay() {
      //删除复选框选中的信息
      if (this.replayboolen == true) {
        this.$confirm("确定要删除这条信息吗？")
          .then((_) => {
            this.axios.delete("/replay/" + this.replayid).then((res) => {
              this.getReplay();
              this.replayboolen = false;
            });
          })
          .catch((err) => {
            this.replay.checked = false;
            console.log(err);
          });
      } else {
        return;
      }
    },

    playback() {
      //配置的回放
      this.dialogreplay = false;
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
  },
};
</script>

<style scoped>
.active {
  border-bottom: 3px solid rgb(37, 141, 222) !important;
}
.home {
  width: 100%;
  height: 100%;
  position: relative;
  z-index: 1;
}
.hearder {
  width: 100%;
  height: 10rem;
  background-color: rgb(245, 124, 0);
  display: flex;
  justify-content: space-around;
  align-items: center;
}
.hearder > .left {
  width: 40%;
  height: 5rem;
  /* background-color: blue; */
  color: white;
  line-height: 5rem;
  font-size: 2rem;
}
.hearder > .right {
  width: 27%;
  height: 8rem;
  background: url("../assets/logo.png") no-repeat center;
  background-size: 100%;
}

.nav {
  width: 100%;
  height: 5rem;
  background-color: rgb(245, 124, 0);
  /* background-color:pink; */
  list-style: none;
  display: flex;
  justify-content: space-between;
  align-items: center;
  color: white;
}
.nav > li {
  width: 33%;
  height: 5rem;
  /* background-color: aqua; */
  text-align: center;
  line-height: 5rem;
  font-size: 1.5rem;
}
.home>>>.nav>a{
    width: 33%;
  height: 5rem;
  /* background-color: aqua; */
  text-align: center;
  line-height: 5rem;
  font-size: 1.5rem;
  text-decoration: none;
  color: white;
}
.router-link-exact-active{
  border-bottom: 3px solid rgb(37, 141, 222) !important;

}
</style>