<template>
  <div id="app">
    <Game />
  </div>
</template>

<script>
import Game from "./components/Game.vue";
import "./assets/styles/Template.sass";

export default {
  name: "App",
  components: {
    Game,
  },
};
</script>
