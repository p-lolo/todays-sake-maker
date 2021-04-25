<template>
  <div id="app">
    <img alt="Vue logo" src="./assets/logo.png" />
    <todays-sake v-model="picture" />
    <img :src="picture" />
  </div>
</template>

<script>
import TodaysSake from "./components/TodaysSake.vue";

export default {
  name: "App",
  components: {
    TodaysSake,
  },
  data() {
    return {
      picture: null,
    };
  },
};
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
