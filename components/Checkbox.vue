<template>
  <div class="bs-checkbox " style="width: 36px; ">
    <div class="th-inner ">
      <input name="btSelectAll" type="checkbox" :checked='item.checkbox' @click="select">
    </div>
    <div class="fht-cell"></div>
  </div>
</template>

<script>
export default {
  // 接收上层传递的value
  props: ['item','index','page'],
  data() {
    return {
    }
  },
  created() {
  },
  methods: {
    select(e) {
      this.item.checkbox = e.target.checked;
    }
  }

}

</script>

<style lang="stylus" rel="stylesheet/stylus">

</style>