<template>
  <div class="synched-columns">
      <main-column class="column main">
          <div v-for="n in 10">{{ n }}</div>
      </main-column>
      <sub-column class="column sub first">
          <div v-for="n in 10">{{ n }}</div>
      </sub-column>
      <sub-column class="column sub second">
          <div v-for="n in 10">{{ n }}</div>
      </sub-column>
  </div>
</template>

<script>
import Vue from 'vue';

const MainColumn = Vue.component('main-column', {
  template: '<div><slot></slot></div>',
  data() {
    return {};
  },
});

const SubColumn = Vue.component('sub-column', {
  template: '<div :style="styleObject"><slot></slot></div>',
  data() {
    return {
      styleObject: { top: '0' },
      winOffset: 0,
    };
  },
  methods: {
    syncscroll({ top, bottom }) {
      if (top <= 0 && bottom >= 0) {
        const scrollSpeed = this.winOffset / this.$parent.winOffset;
        this.styleObject.top = `${(scrollSpeed * top) - top}px`;
        window.console.log(`top: ${this.styleObject.top}`);
      } else if (top > 0) {
        this.styleObject.top = '0';
      } else if (bottom < 0) {
        this.styleObject.top = `${-1 * (this.$el.offsetHeight - this.$parent.$el.offsetHeight)}px`;
      }
    },
    resize() {
      this.winOffset = this.$el.offsetHeight - window.innerHeight;
    },
  },
  mounted() {
    this.$parent.$on('syncscroll', this.syncscroll);
    this.resize();
  },
  beforeDestroy() {
    this.$parent.$off('syncscroll', this.syncscroll);
  },
});

export default {
  name: 'synched-columns',
  components: { MainColumn, SubColumn },
  data() {
    return {
      winOffset: 0,
    };
  },
  methods: {
    scroll() {
      const boundingClientRect = this.$el.getBoundingClientRect();
      const top = boundingClientRect.top;
      const bottom = boundingClientRect.bottom - window.innerHeight;
      this.$emit('syncscroll', { top, bottom });
    },
    resize() {
      this.winOffset = this.$el.offsetHeight - window.innerHeight;
    },
  },
  mounted() {
    window.addEventListener('scroll', this.scroll);
    this.resize();
  },
  beforeDestroy() {
    window.removeEventListener('scroll', this.scroll);
  },
};
</script>

<style scoped>
.synched-columns {
    width: 100%;
    height: auto;
    display: flex;
    position: relative;
}
.column {
    width: 33%;
    display: flex;
    flex-direction: column;
    justify-content: space-around;
    color: white;
}
.column.main {
    height: 1500px;
    background-color: red;
}
.column.sub {
    position: absolute;
    top: 0;
}
.column.sub.first {
    height: 2000px;
    background-color: green;
    left: 33%;
}
.column.sub.second {
    height: 1000px;
    background-color: blue;
    left: 66%;
}
</style>
