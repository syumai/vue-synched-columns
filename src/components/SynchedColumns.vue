<template>
  <div class="synched-columns" :style="styleObject">
      <column class="first">
          <div v-for="n in 10">{{ n }}</div>
      </column>
      <column class="second">
          <div v-for="n in 10">{{ n }}</div>
      </column>
      <column class="third">
          <div v-for="n in 10">{{ n }}</div>
      </column>
  </div>
</template>

<script>
import Vue from 'vue';

(() => {
  const requestAnimationFrame = window.requestAnimationFrame || window.mozRequestAnimationFrame ||
    window.webkitRequestAnimationFrame || window.msRequestAnimationFrame;
  window.requestAnimationFrame = requestAnimationFrame;
})();

const Column = Vue.component('column', {
  template: '<div :class="classList" :style="styleObject"><slot></slot></div>',
  data() {
    return {
      styleObject: { transform: 'translateY(0px)' },
      shortest: false,
      winOffset: 0,
    };
  },
  computed: {
    classList() {
      return this.shortest ? ['column', 'main'] : ['column', 'sub'];
    },
  },
  methods: {
    syncscroll({ top, bottom }) {
      if (!this.shortest) {
        if (top <= 0 && bottom >= 0) {
          const scrollSpeed = this.winOffset / this.$parent.winOffset;
          this.styleObject.transform = `translateY(${(scrollSpeed * top) - top}px)`;
        } else if (top > 0) {
          this.styleObject.transform = 'translateY(0px)';
        } else if (bottom < 0) {
          this.styleObject.transform = `translateY(${-1 * (this.$el.offsetHeight - this.$parent.$el.offsetHeight)}px)`;
        }
      }
    },
    resize() {
      this.winOffset = this.$el.offsetHeight - window.innerHeight;
    },
  },
  mounted() {
    window.addEventListener('resize', this.resize);
    this.$parent.$on('syncscroll', this.syncscroll);
    this.resize();
  },
  beforeDestroy() {
    window.removeEventListener('resize', this.resize);
    this.$parent.$off('syncscroll', this.syncscroll);
  },
});

export default {
  name: 'synched-columns',
  components: { Column },
  data() {
    return {
      winOffset: 0,
      styleObject: { height: '0px' },
      shortestChild: null,
      request: null,
    };
  },
  methods: {
    scroll() {
      const boundingClientRect = this.$el.getBoundingClientRect();
      const top = boundingClientRect.top;
      const bottom = boundingClientRect.bottom - window.innerHeight;
      this.$emit('syncscroll', { top, bottom });
      window.requestAnimationFrame(this.scroll);
    },
    resize() {
      this.winOffset = this.shortestChild.$el.offsetHeight - window.innerHeight;
    },
  },
  mounted() {
    window.addEventListener('resize', this.resize);
    this.request = window.requestAnimationFrame(this.scroll);
    this.shortestChild = this.$children.sort((a, b) => (
      a.$el.offsetHeight - b.$el.offsetHeight
    ))[0];
    this.shortestChild.shortest = true;
    this.styleObject.height = `${this.shortestChild.$el.offsetHeight}px`;
    this.resize();
  },
  beforeDestroy() {
    window.removeEventListener('resize', this.resize);
    window.cancelAnimationFrame(this.request);
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
.column.first {
    height: 1500px;
    background-color: red;
}
.column.second {
    background-color: green;
    height: 2000px;
}
.column.third {
    background-color: blue;
    height: 1000px;
}
</style>
