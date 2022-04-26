
<template>
  <div class="home">
    <div class="hearder">
      <div class="right"></div>
      <div class="left">Recording and Replayback System(RPS)&nbsp;<span id="v">V0.0.02</span></div>
    </div>

    <div class="nav">
      <router-link to="/home/boardinfo">Boardinfo</router-link>
      <router-link to="/home/record">Record</router-link>
      <router-link to="/home/replay">Replay</router-link>
    </div>

    <keep-alive>
      <router-view/>
    </keep-alive>
    
  </div>
</template>

<script>
export default {
  data() {
    return {

    };
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
  height: 8rem;
  background-color: #C0C4CC;
  display: flex;
  justify-content: space-around;
  align-items: center;
  padding-bottom: 2rem;
  padding-top: 1rem;
}
.hearder > .left {
  width: 55%;
  height: 5rem;
  /* background-color: blue; */
  color: rgb(37,91,150);
  line-height: 5rem;
  font-size: 2rem;
  font-size:  2rem;
}
.hearder > .right {
  width: 27%;
  height: 8rem;
  background: url("../assets/logo2.png") no-repeat center;
  background-size: 96%;
}
#v{
  font-size: 1rem;
}
.nav {
  width: 100%;
  height: 5rem;
  background-color: #C0C4CC;
  /* background-color:pink; */
  list-style: none;
  display: flex;
  justify-content: space-between;
  align-items: center;
  color: rgb(37,91,150);
}
.nav > li {
  width: 33%;
  height: 5rem;
  /* background-color: aqua; */
  text-align: center;
  line-height: 5rem;
  font-size: 1.5rem;
}
.home >>> .nav > a {
  width: 33%;
  height: 5rem;
  /* background-color: aqua; */
  text-align: center;
  line-height: 5rem;
  font-size: 1.5rem;
  text-decoration: none;
  color: rgb(37,91,150);
}
.router-link-exact-active {
  border-bottom: 3px solid rgb(37, 141, 222) !important;
}

@media screen and (min-width:100px) and (max-width: 1297px) {
  .hearder > .left{
    font-size: 1.5rem;
  }
}
</style>