<template lang="pug">
.modal.fixed.w-screen.h-screen.top-0.left-0.z-10
  .modal__body.container.w-96.h-60.absolute.inset-0.m-auto.z-30.p-8.bg-white.rounded-lg
    input.modal__input.pointer-events-auto.border-solid.border-2.border-black-100.p-2(
      ref='barcode'
      v-model='barcodeData'
      )
    button.pointer-events-auto.px-4.py-2.bg-blue-900.text-white.ml-2.rounded-lg(@click='close')
      |close
    p.text-xs.pt-2
      |※モーダルを閉じるとデータがクリアされます。
      br
      |※取得できない場合は直接入力してください。
    p.font-bold
      |入力されたデータ
      button.pointer-events-auto.px-4.ml-2.bg-blue-500.text-white.rounded-lg(
        v-if='barcodeData'
        @click='clear'
        )
        |clear
    p
      |{{ barcodeData }}
    .w-80.absolute.bottom-8
      |{{ msg }}
  .modal__bg.absolute.w-full.h-full.top-0.left-0.z-20.bg-black.opacity-50(@click='close')
</template>
  
<script lang="ts">
import Vue from 'vue'

export default Vue.extend({
  name: 'Modal',
  data: () => ({
    barcodeData: '',
    msg: ''
  }),
  methods: {
    setFocus() {
      const refs: any = this.$refs
      refs.barcode.focus()
    },
    close() {
      this.barcodeData = ''
      this.msg = ''
      this.$emit("close")
    },
    clear() {
      this.barcodeData = ''
      this.msg = ''
      this.setFocus()
    }
  },
  watch: {
    barcodeData(newData) {
      if (newData.length > 0) {
        const refs: any = this.$refs
        if (newData.length == 13 && !isNaN(parseInt(newData))) {
          refs.barcode.blur()
          this.msg = 'フォーマットに則ったデータです。clearボタンを押すまで入力できません。'
        } else {
          this.msg = 'フォーマットに則ったデータではありません'
        }
      }
    }
  }
})
</script>
