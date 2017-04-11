<template>
  <span>
        <img :src="it.param.url" :style="style" />{{ it.value }}
      </span>
</template>

<script>
export default {
  // 接收上层传递的value
  props: ['it'],
  data() {
    return {
      style: ''
    }
  },
  created() {
    if (this.it.param.width) {
      this.style += 'width:' + this.it.param.width + ";";
    }
    if (this.it.param.height) {
      this.style += 'height:' + this.it.param.height + ";";
    }
  },
  methods: {
  }

}

</script>

<style lang="stylus" rel="stylesheet/stylus">

</style>