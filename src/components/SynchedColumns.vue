<script>
(() => {
  const requestAnimationFrame = window.requestAnimationFrame || window.mozRequestAnimationFrame ||
    window.webkitRequestAnimationFrame || window.msRequestAnimationFrame;
  window.requestAnimationFrame = requestAnimationFrame;
})();

const Column = {
  name: 'column',
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
};

const SynchedColumns = {
  name: 'synched-columns',
  template: '<div class="synched-columns" :style="styleObject"><slot></slot></div>',
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

export default {
  Column,
  SynchedColumns,
};

</script>
