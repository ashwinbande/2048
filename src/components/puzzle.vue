<template>
  <div class="puzzle__container">
    <div
      class="puzzle__box"
      :style="{
        'grid-template-columns': '1fr '.repeat(size),
        'grid-template-rows': '1fr '.repeat(size),
      }"
    >
      <template v-for="(number, index) in state">
        <div
        :key="index"
        class="puzzle__tile"
        :class="`color-${number > 2048 ? 2048 : number}`"
        >
          <span v-if="number !== 0">{{ number }}</span>
        </div>
      </template>
    </div>
  </div>
</template>

<script>
export default {
  name: 'puzzle',
  props: {
    size: {
      type: Number,
      required: false,
      default: 3,
    },
  },
  data() {
    return {
      state: [],
      anyMove: false,
      score: 0,
    };
  },
  methods: {
    addTile() {
      this.$emit('score', this.score);
      this.score = 0;
      if (!this.anyMove) return;
      const position = Math.floor(Math.random() * this.state.length);
      if (this.state[position] !== 0) this.addTile();
      else {
        const nextTile = [2, 2, 2, 4][Math.floor(Math.random() * 4)];
        this.$set(this.state, position, nextTile || 2);
        this.anyMove = false;
      }
    },
    moveToTop(i) {
      const current = i;
      const destination = i - this.size;
      if (destination < 0) return;
      if (this.state[destination] === 0 && this.state[current] === 0) return;
      if (this.state[destination] !== this.state[current] && this.state[destination] !== 0) return;
      if (this.state[destination] === 0) {
        this.$set(this.state, destination, this.state[current]);
        this.$set(this.state, current, 0);
        this.moveToTop(destination);
      } else if (this.state[current] === this.state[destination]) {
        this.$set(this.state, destination, this.state[destination] * 2);
        this.$set(this.state, current, 0);
        this.score += this.state[destination];
      }
      this.anyMove = true;
    },
    arrowUp() {
      for (let i = 0; i < this.state.length; i += 1) {
        this.moveToTop(i);
      }
      this.addTile();
    },
    moveToBottom(i) {
      const current = i;
      const destination = i + this.size;
      if (destination > this.state.length - 1) return;
      if (this.state[destination] === 0 && this.state[current] === 0) return;
      if (this.state[destination] !== this.state[current] && this.state[destination] !== 0) return;
      if (this.state[destination] === 0) {
        this.$set(this.state, destination, this.state[current]);
        this.$set(this.state, current, 0);
        this.moveToBottom(destination);
      } else if (this.state[current] === this.state[destination]) {
        this.$set(this.state, destination, this.state[destination] * 2);
        this.$set(this.state, current, 0);
        this.score += this.state[destination];
      }
      this.anyMove = true;
    },
    arrowDown() {
      for (let i = this.state.length - 1; i >= 0; i -= 1) {
        this.moveToBottom(i);
      }
      this.addTile();
    },
    moveToRight(i) {
      const current = i;
      const destination = i + 1;
      if ((current % this.size) + 1 === this.size) return;
      if (this.state[destination] === 0 && this.state[current] === 0) return;
      if (this.state[destination] !== this.state[current] && this.state[destination] !== 0) return;
      if (this.state[destination] === 0) {
        this.$set(this.state, destination, this.state[current]);
        this.$set(this.state, current, 0);
        this.moveToRight(destination);
      } else if (this.state[current] === this.state[destination]) {
        this.$set(this.state, destination, this.state[destination] * 2);
        this.$set(this.state, current, 0);
        this.score += this.state[destination];
      }
      this.anyMove = true;
    },
    arrowRight() {
      for (let i = this.state.length - 1; i >= 0; i -= 1) {
        this.moveToRight(i);
      }
      this.addTile();
    },
    moveToLeft(i) {
      const current = i;
      const destination = i - 1;
      if ((current % this.size) === 0) return;
      if (this.state[destination] === 0 && this.state[current] === 0) return;
      if (this.state[destination] !== this.state[current] && this.state[destination] !== 0) return;
      if (this.state[destination] === 0) {
        this.$set(this.state, destination, this.state[current]);
        this.$set(this.state, current, 0);
        this.moveToLeft(destination);
      } else if (this.state[current] === this.state[destination]) {
        this.$set(this.state, destination, this.state[destination] * 2);
        this.$set(this.state, current, 0);
        this.score += this.state[destination];
      }
      this.anyMove = true;
    },
    arrowLeft() {
      for (let i = 0; i < this.state.length; i += 1) {
        this.moveToLeft(i);
      }
      this.addTile();
    },
  },
  watch: {
    size: {
      immediate: true,
      handler(s) {
        this.state = Array.from(Array(s * s), () => 0);
        this.anyMove = true;
        this.addTile();
      },
    },
  },
  created() {
    document.addEventListener('keydown', (e) => {
      if (e.key === 'ArrowUp') this.arrowUp();
      if (e.key === 'ArrowDown') this.arrowDown();
      if (e.key === 'ArrowRight') this.arrowRight();
      if (e.key === 'ArrowLeft') this.arrowLeft();
    });
  },
};
</script>

<style lang="scss">
  .puzzle__container {
    flex: 1;
    width: 30vw;
    height: 30vw;
    display: block;
    margin: 6px auto;
    border: 2px solid rgb(187, 173, 160);
    padding: 6px;
    border-radius: 6px;
    box-shadow: 3px 3px 10px 6px rgba(0, 0, 0, 0.5);
    background-color: rgb(187, 173, 160);
    .puzzle__box {
      // margin: 6px;
      height: 100%;
      display: grid;
      grid-gap: 6px;
      background-color: rgb(187, 173, 160);
      .puzzle__tile {
        background-color: rgba(238, 228, 218, 0.35);
        border-radius: 6px;
        display: flex;
        justify-content: center;
        align-items: center;
        // color: white;
        cursor: pointer;
        user-select: none;
        font-size: 36px;
        font-weight: 600;
        &.color-2 {
          background-color: rgb(238, 228, 218);
        }
        &.color-4 {
          background-color: rgb(237, 224, 200);
        }
        &.color-8 {
          background-color: rgb(242, 177, 121);
        }
        &.color-16 {
          background-color: rgb(245, 149, 99);
        }
        &.color-32 {
          background-color: rgb(246, 124, 95);
        }
        &.color-64 {
          background-color: rgb(246, 94, 59);
        }
        &.color-128 {
          background-color: rgb(237, 207, 114);
        }
        &.color-256 {
          background-color: rgb(237, 204, 97);
        }
        &.color-512 {
          background-color: rgb(237, 200, 80);
        }
        &.color-1024 {
          background-color: rgb(237, 197, 63);
        }
        &.color-2048 {
          background-color: rgb(237, 194, 46);
        }
      }
    }
  }
  @media only screen and (max-width: 640px) {
    .puzzle__container {
      width: 95vw;
      height: 95vw;
    }
  }
</style>
