<template>
  <div class="carousel">
    <div class="carousel__container">
      <div
        class="carousel__pages"
        >
        <div
          v-for="(item, index) in items"
        >
          <div
            :class="{ carousel__page: true }"
            :style="styles(index)"
            >{{ item.label }} : {{ item.time }}秒</div>
        </div>
      </div>
    </div>
    <div class="carousel__dots">
      <span
        v-for="(item, index2) in items"
        :class="{ carousel__dot: true, 'current': isCurrent(index2) }"
        :key="index2"
      >
      </span>
    </div>
    <div class="carousel__arrow">
      <button @click="prev">＜</button>
      <button @click="next">＞</button>
    </div>
  </div>
</template>

<script lang="ts">
import Vue from 'vue'
export default Vue.extend({
  name: "HelloWorld",
  data: () => ({
    current: 0,
    pageWidth: 200,
    blindfold: false,
    timeoutID: 0,
    items: [
      {
        label: "1",
        time: 3,
      },
      {
        label: "2",
        time: 0.5,
      },
      {
        label: "3",
        time: 5,
      },
      {
        label: "4",
        time: 1,
      },
      {
        label: "5",
        time: 2,
      }
    ]
  }),
  mounted() {
    this.$nextTick(() => {
      this.autoScroll()
    })
  },
  methods: {
    autoScroll() {
      this.timeoutID = window.setTimeout(() => {
          this.next()
          this.autoScroll()
      }, this.items[this.current].time * 1000);
    },
    next() {
      window.clearTimeout(this.timeoutID)
      this.current === this.items.length - 1 ? this.current = 0 : this.current++
      this.autoScroll()
      return 
    },
    prev() {
      window.clearTimeout(this.timeoutID)
      this.current === 0 ? this.current = this.items.length - 1 : this.current--
      this.autoScroll()
      return 
    },
    isCurrent(page: number) {
      return this.current === page
    },
    isPPrev(page:number) {
      let num = page + 2
      if (num >= this.items.length) num -= this.items.length
      return num
    },
    isPrev(page:number) {
      let num = page + 1
      if (num === this.items.length) num = 0
      return num
    },
    isNext(page:number) {
      let num = page - 1
      if (num < 0) num = this.items.length - 1
      return num 
    },
    isNNext(page:number) {
      let num = page - 2
      if (num < 0) num += this.items.length - 1
      return num
    },
    styles(num: number): { [key: string]: string } {
      const width = 200
      let n = 3
      let z = 10
      if (this.current === this.isPPrev(num)) {
        n = -2
        z = 10
      } else if (this.current === this.isPrev(num)) {
        n = -1
        z = 20
      } else if (this.current === num) {
        n = 0
        z = 20
      } else if (this.current === this.isNext(num)) {
        n = 1
        z = 20
      } else if (this.current === this.isNNext(num)) {
        n = 2
        z = 10
      } else {
        n = 3
        z = 10
      }
      return {
        transform: `translate(${n * width}px`,
        zIndex: String(z),
      }
    },
  },
})
</script>

<style lang="scss" scoped>
.carousel {
  width: 200px;
  &__container {
    clip-path: inset(0);
    position: relative;
    height: 200px;
    width: 200px;
    display: inline-block;
    margin-left: auto;
    margin-right: auto;
  }
  &__pages {
    position: relative;
  }
  &__page {
    position: absolute;
    top: 0;
    left: 0;
    width: 200px;
    height: 200px;
    background-color: #DDD;
    border: 1px solid #FFF;
    box-sizing: border-box;
    transition-property: transform;
    transition-duration: 0.5s;
    z-index: 10;
    font-size: 50px;
  }
  &__dots {
    display: flex;
    justify-content: center;
    margin: auto;
    margin-bottom: 16px;
  }
  &__dot {
    height: 8px;
    width: 8px;
    background-color: #AAA;
    border-radius: 50%;
    display: inline-block;
    &.current {
      background-color: rgb(154, 225, 253);
    }
  }
  &__arrow { 
    text-align: center;
  }
}
</style>