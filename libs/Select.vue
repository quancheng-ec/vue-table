<template>
  <div>
    <select v-model="item[it.name]">
      <option v-for="option in it.param.options"
              :value="option.value">{{ option.label }}</option>
    </select>
  </div>
</template>

<script>
export default {
  data() {
    return {
    }
  },
  computed: {
  },
  props: [
    'item', 'it'
  ],
  methods: {
  }
}
</script>

<style lang="stylus" rel="stylesheet/stylus">
</style>