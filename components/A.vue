<template>
  <a :href="it.param.href" :target="it.param.target?it.param.target:''">{{ it.value }}</a>
</template>

<script>
export default {
  // 接收上层传递的value
  props: ['it'],
  data() {
    return {
    }
  },
  methods: {
    update(value) {
      this.val = value;
      this.$emit('a', value);
    }
  }

}

</script>

<style lang="stylus" rel="stylesheet/stylus">

</style>