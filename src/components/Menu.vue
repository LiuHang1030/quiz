<template>
  <div>
    <button @click="playBtnClickHandle">开始答题</button>
  </div>
</template>

<script>
export default {
  methods: {
    playBtnClickHandle() {
      this.$emit("playClick");
    },
  },
};
</script>

<style>
</style>