<template>
  <div id="app">
    <div class="header">
      <h1>2048</h1> {{ score }}
    </div>
    <div class="puzzle">
      <puzzle :size="4" @score="addScore"></puzzle>
    </div>
    <div class="footer">
      <h4><a href="https://github.com/ashwinbande">© Ashwin Bande</a></h4>
    </div>
  </div>
</template>

<script>
import Puzzle from '@/components/puzzle.vue';

export default {
  name: 'App',
  components: { Puzzle },
  data() {
    return {
      score: 0,
    };
  },
  methods: {
    addScore(score) {
      this.score += score;
    },
  },
};
</script>

<style lang="scss">
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
}
body {
  padding: 0;
  margin: 0;
  background-color: #f3f379;
}
.app {
  display: flex;
  flex-direction: column;
}
</style>
