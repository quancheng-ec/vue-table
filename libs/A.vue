<template>
  <a :href="getHref()"
     :target="it.param.target?it.param.target:''">{{ item[it.name] }}</a>
</template>

<script>
export default {
  // 接收上层传递的value
  props: ['it', 'item'],
  data() {
    return {
    }
  },
  methods: {
    getHref() {
      if (typeof this.it.param.href === 'function') {
        return this.it.param.href.apply(this);// this.it.param.href(this.item);
      }
      return it.param.href;
    }
  }

}

</script>

<style lang="stylus" rel="stylesheet/stylus">

</style>