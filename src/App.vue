<template>
  <div id="app">
    <left-nav></left-nav>
    <div class="main">
      <router-view></router-view>
    </div>
  </div>
</template>

<script>
import leftNav from "@/components/common/leftNav";
export default {
  name: "App",
  components: {
    leftNav
  }
};
</script>

<style>
#app {
  font-family: "Microsoft YaHei", Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
}
.main {
  float: left;
  width: 95%;
  height: 100%;
  background-color: #EFF2F7;
  overflow: hidden;
}
</style>
