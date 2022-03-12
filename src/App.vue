<template>
  <div id="app">
    <game-board />
  </div>
</template>

<script>
import GameBoard from "./components/GameBoard.vue";

export default {
  name: "App",
  data() {
    return {
      testEmoji: "🙂",
    };
  },
  components: {
    GameBoard,
  },
};
</script>

<style></style>
