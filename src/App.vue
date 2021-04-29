<template>
  <v-app>
    <div id="app">
      <v-app-bar color="primary" dark app>
        <v-toolbar-title>今の酒はこれ！ジェネレーター</v-toolbar-title>
        <v-spacer></v-spacer>
        <v-toolbar-items>
          <v-btn text>Button</v-btn>
          <v-btn text>Button</v-btn>
        </v-toolbar-items>
      </v-app-bar>
      <img alt="Vue logo" src="./assets/logo.png" />
      <todays-sake />
    </div>
    <v-footer color="primary" dark> Vuetify </v-footer>
  </v-app>
</template>

<script>
import TodaysSake from "./components/TodaysSake.vue";

export default {
  name: "App",
  components: {
    TodaysSake,
  },
  data() {
    return {};
  },
};
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
