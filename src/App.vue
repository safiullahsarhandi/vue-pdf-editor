<template>
  <v-app>
    <AppBar />
    <SideMenu />
    <v-main class="grey darken-1">
      <v-container fluid>
        <PlayGround />
      </v-container>
    </v-main>
  </v-app>
</template>

<script>
import SideMenu from "./components/SideMenu.vue";
import AppBar from "./components/AppBar.vue";
import PlayGround from "./components/PlayGround";

export default {
  name: "App",
  components: {
    SideMenu,
    AppBar,
    PlayGround,
  },
};
</script>





