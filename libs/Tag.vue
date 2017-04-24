<template>
  <div>
    <template v-if="item[it.name]">
      <span class="tag label label-default text-muted qc-label"
            v-for="(itt,index) in item[it.name]" v-if="itt.label">{{itt.label}}
       </span>
    </template>
  </div>
</template>

<script>

export default {
  props: ['it', 'item'],
  data() {
    return {
    }
  },
  methods: {

  },
  components: {
  }

}

</script>

<style lang="stylus" rel="stylesheet/stylus">

</style>