<template>
  <v-app>
    <div id="app">
      <v-app-bar color="primary" flat dark app>
        <v-toolbar-title>今の酒はこれ！ジェネレーター</v-toolbar-title>
        <v-spacer></v-spacer>
        <v-toolbar-items>
          <v-btn
            icon
            href="https://github.com/p-lolo/todays-sake-maker"
            target="_blank"
            ><v-icon>mdi-github</v-icon></v-btn
          >
          <v-btn icon href="https://twitter.com/p_lolo" target="_blank"
            ><v-icon>mdi-twitter</v-icon></v-btn
          >
        </v-toolbar-items>
      </v-app-bar>
      <todays-sake />
    </div>
    <v-footer color="primary" dark></v-footer>
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
  font-family: "Kiwi Maru", Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  color: #2c3e50;
  margin-top: 40px;
}
.v-application {
  font-family: "Kiwi Maru" !important;
}
</style>
