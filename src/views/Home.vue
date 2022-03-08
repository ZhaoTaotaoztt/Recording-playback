
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
.home >>> .nav > a {
  width: 33%;
  height: 5rem;
  /* background-color: aqua; */
  text-align: center;
  line-height: 5rem;
  font-size: 1.5rem;
  text-decoration: none;
  color: white;
}
.router-link-exact-active {
  border-bottom: 3px solid rgb(37, 141, 222) !important;
}
</style>