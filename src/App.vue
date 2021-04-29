<template>
  <v-app>
    <div id="app">
      <v-app-bar color="primary" dark app>
        <v-toolbar-title>今の酒はこれ！ジェネレーター</v-toolbar-title>
        <v-spacer></v-spacer>
        <!-- <v-toolbar-items>
          <v-btn text>Button</v-btn>
          <v-btn text>Button</v-btn>
        </v-toolbar-items> -->
      </v-app-bar>
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
@import url('https://fonts.googleapis.com/css2?family=Kiwi+Maru:wght@400;500&family=M+PLUS+1p&family=M+PLUS+Rounded+1c&family=New+Tegomin&family=Noto+Sans+JP&family=Potta+One&family=Yusei+Magic&display=swap');
#app {
  font-family: "Kiwi Maru", Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  color: #2c3e50;
  margin-top: 60px;
}
.v-application {
  font-family: "Kiwi Maru" !important;
}
</style>
