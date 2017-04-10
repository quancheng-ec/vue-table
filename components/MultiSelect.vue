<template>
  <ul class="list-group list-group-full"
      :style="hierarchy>0?'':'height:150px;overflow-y:auto;'">
    <template v-for="item in data">
      <li class="list-group-item"
          :style="'padding-left:'+(hierarchy*30)+'px'">
        <i @click="clickNode(item)"
           :class="'fa fa-'+(item.children?item.show?'caret-down':'caret-right':'angle-right')"></i>
  
        <input v-if="isSingle"
               type="radio"
               :name="'qc-multiselect-'+tempId"
               style="margin:0 2px;"
               :value="item.id"
               v-model="item.selected"
               @click="selectNode(item)">
        <input v-if="!isSingle"
               type="checkbox"
               style="margin:0 2px;"
               :checked="item.selected"
               v-model="item.selected"
               @click="selectNode(item)">
        <label @click="clickNode(item)" style="display:inline-block;">{{item.label}}</label>
      </li>
      <template v-if="item.children && item.show">
        <multi-select :index='(parseInt(hierarchy)+1)'
                      :data="item.children"
                      :isSingle="isSingle"></multi-select>
      </template>
  
    </template>
  </ul>
</template>

<script>

import MultiSelect from './MultiSelect.vue'

export default {
  props: ['data', 'index', 'isSingle', 'tempId'],
  data() {
    return {
      hierarchy: 0
    }
  },
  beforeCreate: function () {
    this.$options.components.MultiSelect = require('./MultiSelect.vue')
  },
  created() {
    if (this.index) {
      this.hierarchy = this.index;
    }
  },
  methods: {
    clickNode(item) {
      item.show = !item.show;
      if (!item.disableParentSelect && (!item.children || item.children.length === 0)) {
        item.selected = !item.selected;
      }
    },
    selectNode(item) {
      if (item.disableParentSelect && item.children && item.children.length > 0) {
        item.selected = false;
        item.show = !item.show;
        return;
      }
      this.eachItems(item, item.selected);
      if (this.isSingle) {
        this.$emit('select');
      }
    },
    eachItems(item, isSelected) {
      if (!item.children) {
        return;
      }
      item.show = isSelected;
      item.children.forEach(it => {
        it.show = !item.show;
        it.selected = isSelected;
        this.eachItems(it, isSelected);
      });
    }
  },
  components: {
    MultiSelect
  }

}

</script>

<style lang="stylus" rel="stylesheet/stylus">
.list-group{
  margin-bottom:0px
}
.list-group-item{
  border:0px
  padding:2px 15px;
}
.qc-multiselect{
  //padding-left:5px;
}
</style>