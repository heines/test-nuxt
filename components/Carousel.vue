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
            >
            <img v-show="item.type === 1" :src="item.url" />
            <video v-show="item.type === 2" muted ref="test">
              <source :src="item.url" type='video/mp4' />
            </video>
            <youtube
              v-show="item.type === 3"
              ref="youtube"
              :video-id="item.url"
              :playerVars="playerVars"
              width="800"
              height="450"
              />
            <span>{{ index }} / {{ item.label }} : {{ item.time }}秒</span>
          </div>
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
    pageWidth: 800,
    blindfold: false,
    timeoutID: 0,
    playerVars: {
      rel: 0,
      controls: 0,
      mute: 1,
      origin: ''
    },
    items: [
      {
        label: "5",
        time: 1,
        url: '/testmovie.mp4',
        type: 2
      },
      {
        label: "1",
        time: 10,
        url: 'zkNzxsaCunU',
        type: 3
      },
      {
        label: "2",
        time: 10,
        url: 'Gtku_jsNgAA',
        type: 3
      },
      {
        label: "3",
        time: 1,
        url: 'https://placehold.jp/800x450.png',
        type: 1
      },
      {
        label: "4",
        time: 0.5,
        url: 'https://placehold.jp/3d4070/ffffff/800x450.jpg?text=jpg',
        type: 1,
      },
    ]
  }),
  mounted() {
    this.playerVars.origin = window.location.origin
    window.setTimeout(() => {
      this.videoCtrl()
      this.$nextTick(() => {
        this.autoScroll()
      })
    }, 100)
  },
  methods: {
    autoScroll() {
      this.timeoutID = window.setTimeout(() => {
          this.next()
          this.autoScroll()
          this.videoCtrl()
      }, this.items[this.current].time * 1000);
    },
    refs():any {
      return this.$refs
    },
    videoCtrl() {
      const tmp = this.current - 1
      const prevNum = tmp >= 0 ? tmp : this.items.length - 1
      if (this.items[this.current].type === 2) {
        this.refs().test[this.current].play()
      } else if (this.items[this.current].type === 3) {
        this.refs().youtube[this.current].player.playVideo()
      }
      if (this.items[prevNum].type === 2) {
        this.refs().test[prevNum].pause()
        window.setTimeout(() => {
          this.refs().test[prevNum].currentTime = 0
        }, 500)
      } else if (this.items[prevNum].type === 3) {
        this.refs().youtube[prevNum].player.pauseVideo()
        window.setTimeout(() => {
          this.refs().youtube[prevNum].player.seekTo(0, true)
        }, 500)
      }
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
        transform: `translate(${n * this.pageWidth}px`,
        zIndex: String(z),
      }
    },
  },
})
</script>

<style lang="scss" scoped>
.carousel {
  width: 800px;
  &__container {
    position: relative;
    width: 800px;
    height: 450px;
    display: inline-block;
    margin-left: auto;
    margin-right: auto;
    overflow: hidden;
  }
  &__pages {
    position: relative;
  }
  &__page {
    position: absolute;
    top: 0;
    left: 0;
    width: 800px;
    height: 450px;
    background-color: #DDD;
    border: 1px solid #FFF;
    box-sizing: border-box;
    transition-property: transform;
    transition-duration: 0.5s;
    z-index: 10;
    font-size: 50px;
    overflow: hidden;
    img {
      display: block;
      height: 100%;
    }
    span {
      display: block;
      position: absolute;
      bottom: 0;
      right: 0;
    }
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