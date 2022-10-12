<template lang="pug">
div.m-4
  h1.text-2xl test site
  button.px-4.py-2.bg-blue-900.text-white.rounded-lg(
    @click='openModal'
  )
    |open Modal
  p.text-base
    |コードは
    span.font-bold.text-lg {{ code }}
    |です。
  transition(name="fade")
    Modal(
      ref='child'
      v-show='isModal'
      @close='closeModal'
    )
</template>

<script lang="ts">
import Vue from 'vue'
import Modal from '~/components/Modal.vue';

export default Vue.extend({
  name: "IndexPage",
  components: { Modal },
  data: () => ({
    isModal: false,
  }),
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
    }
  },
  computed: {
    code(): string | (string | null)[] {
      return this.$route.query.code ? this.$route.query.code : "未設定"
    }
  }
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