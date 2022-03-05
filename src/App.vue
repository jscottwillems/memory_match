<template>
  <div id="app">
    <MemoryGame />
  </div>
</template>

<script>
import MemoryGame from "./views/MemoryGame";

export default {
  name: "app",
  components: {
    MemoryGame,
  },
};
</script>

<style lang="scss">
html,
body {
  margin: 0;
  padding: 0;
  font-family: Helvetica, sans-serif;
  overflow-x: hidden;
}
</style>
