<template>
  <div id="app">
    <router-view />
    <go-top :bottom="100" :right="50"></go-top>
  </div>
</template>

<script>
//스크롤 top으로 가는 버튼 library
import GoTop from "@inotom/vue-go-top";
export default {
  name: "App",
  components: {
    GoTop
  }
};
</script>



<style>
#app {
  font-family: "Avenir", Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  /* text-align: center; */
  color: #2c3e50;
  margin: 60px;
}

.logo {
  width: 50px;
}
</style>
