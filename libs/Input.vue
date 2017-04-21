<template>
  <div>
    <div v-if="!edited"
         @mouseover="over"
         @mouseleave="showEdit=false">
      <label> {{ it.prefix }}{{ item[it.name] }}</label>
      <input type="text"
             v-if="edited"
             v-model="value"
             style="width:80px;" />
      <a href="javascript:;"
         v-if="showEdit"
         @click="update"
         title="编辑"><i class="fa fa-pencil"></i></a>
    </div>
    <input type="text"
           v-if="edited"
           v-model="value"
           class="form-control"
           style="width:100;"
           @keyup.enter="save"
           @keyup.esc="edited=false;showEdit=false;showSave=false" />
  </div>
</template>

<script>
export default {
  // 接收上层传递的value
  props: ['it', 'item'],
  data() {
    return {
      value: '',
      edited: false,
      showEdit: false,
      showSave: false
    }
  },
  methods: {
    over() {
      if (this.edited) {
        return;
      }
      this.showEdit = true
    },
    update() {
      this.value = this.item[this.it.name];
      this.edited = true;
      this.showSave = true;
      this.showEdit = false;
    },
    save() {
      this.item[this.it.name] = this.value;
      this.edited = false;
      this.showEdit = false;
      this.showSave = false;
    }
  }

}

</script>

<style lang="stylus" rel="stylesheet/stylus">

</style>