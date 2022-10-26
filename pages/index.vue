<template lang="pug">
div.m-4
  h1.text-2xl test site
  Carousel
  div.h-64
    p.text-base
      |コードは
      span.font-bold.text-lg {{ code }}
      |です。
    button.px-4.py-2.bg-blue-900.text-white.rounded-lg(
      @click='openModal'
    )
      |データの読み取りができない場合
    p.text-base(
      v-if='isSuccess'
    )
      span Enterが検出されました。入力値は以下の通りです。
      br
      |{{ keys }}
    p.text-red-500.text-base(
      v-if='isSuccess'
    )
      |正常にデータを取得できました。以降初期状態に戻すまで入力できません。
    p.text-red-500.text-base.whitespace-pre-wrap(
      v-else-if='isSuccess === false'
    )
      |Enterが検出されましたが想定されないデータ形式です。再度入力を行なってください。
      br
      |{{ errInput }}
    button.px-4.py-2.bg-blue-900.text-white.rounded-lg(
      v-if='isSuccess'
      @click='reset'
    )
      |初期状態に戻します
  button.px-4.py-2.bg-blue-500.text-white.rounded-lg(
    @click='setTest'
  )
    |テストボタン
  p {{ testText }}
  transition(name="fade")
    Modal(
      ref='child'
      v-show='isModal'
      @close='closeModal'
    )
</template>

<script lang="ts">
import test from 'node:test';
import Vue from 'vue'
import Modal from '~/components/Modal.vue';
import Carousel from '~/components/Carousel.vue';

export default Vue.extend({
  name: "IndexPage",
  components: { Modal },
  data: () => ({
    isModal: false as boolean,
    keyCode: '' as string,
    keys: '' as string,
    errInput: '' as string,
    hasData: false as boolean,
    hasCustomer: false as boolean,
    isSuccess: null as boolean | null,
    testText: '' as string,
    clickNum: 0 as number,
    runNum: 0 as number,
  }),
  mounted() {
    document.addEventListener('keydown', this.onKeyDown)
  },
  methods: {
    openModal() {
      this.isModal = true
      this.$nextTick(() => {
        const refs: any = this.$refs
        refs.child.setFocus()
      })
    },
    closeModal() {
      this.isModal = false
    },
    onKeyDown(event: any) {
      this.keyCode = String(event.keyCode)
      if (this.keyCode == '13') {
        this.hasData = true
        this.runNum++
        this.checkInputData()
      } else {
        this.keys += `${event.key}`
      }
    },
    clearData() {
      this.hasCustomer = false
      this.keyCode = ''
      this.keys = ''
      this.hasData = false
    },
    reset() {
      this.clearData()
      this.hasCustomer = false
      this.isSuccess = null
      this.testText = ''
      this.runNum = 0
    },
    checkInputData() {
      if (this.keys.length == 13 && !isNaN(parseInt(this.keys))) {
        this.isSuccess = true
        this.hasCustomer = true
        document.removeEventListener('keydown', this.onKeyDown)
      } else {
        this.isSuccess = false
        this.errInput = this.keys
        this.clearData()
      }
    },
    setTest() {
      const msg = [
        '吾輩は猫である。名前はまだない。',
        '親譲りの無鉄砲で子供の時から損ばかりしている。',
        '山路を登りながら、かう考へた。',
        '私はその人を常に先生と呼んでいた。だからここでもただ先生と書くだけで本名は打ち明けない。',
        '木曽路はすべて山の中である。',
        '或日の暮方の事である。一人の下人が、羅生門の下で雨やみを待っていた。',
        '国境の長いトンネルを抜けると雪国であった。',
      ]
      const index = this.getRandomInt(7)
      this.clickNum++
      this.testText = `${this.clickNum}: ${msg[index]} `
    },
    getRandomInt(max: number) {
      return Math.floor(Math.random() * max);
    }
  },
  computed: {
    code(): string | (string | null)[] {
      return this.$route.query.code ? this.$route.query.code : "未設定"
    }
  },
  watch: {
    hasCustomer(newData, oldData) {
      if (newData !== oldData) {
        if (newData) {
          document.removeEventListener('keydown', this.onKeyDown)
        } else {
          document.addEventListener('keydown', this.onKeyDown)
        }
      }
    },
    isModal(newData, oldData) {
      if (newData !== oldData) {
        if (newData) {
          document.removeEventListener('keydown', this.onKeyDown)
        } else {
          document.addEventListener('keydown', this.onKeyDown)
        }
      }
    },
  },
})
</script>

<style>
.fade-enter-active, .fade-leave-active {
  transition: opacity .5s;
}
.fade-enter, .fade-leave-to /* .fade-leave-active below version 2.1.8 */ {
  opacity: 0;
}
</style>