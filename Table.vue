<template>
  <div >
    <table class="table">
      <thead>
        <tr>
          <th v-for="item in data[0].items" :width="item.width" v-if="!item.hidden" :class="getThClass(item)" :style="getThStyle(item)">
            <template v-if="item.component==='FormCheckbox'">
              <div class="th-inner ">
                <input name="btSelectAll" type="checkbox" @click="selectAll" v-model="checkbox">
              </div>
              <div class="fht-cell"></div>
            </template>
            <template v-else>
              {{item.label}}
            </template>
          </th>
        </tr>
      </thead>
      <tbody>
        <ui-tr v-for="(item,index) in data" :index="index" :item='item' :page="page" v-if='!item.hide' :saveAPI="saveAPI" :deleteAPI="deleteAPI" @refresh='onRefresh(arguments[0])' :checked="checked" :eventBus="eventBus"></ui-tr>
      </tbody>
    </table>
  </div>
</template>

<script>
import UiTr from './components/Tr.vue'
import Vue from "vue"
export default {
  props: ['data', 'page', 'deleteAPI', 'saveAPI', 'checked'],
  data() {
    return {
      eventBus: new Vue(),
      checkbox: false,
      checkedList: []
    }
  },
  created() {
  },
  watch: {
  },
  methods: {
    onRefresh(data) {
      this.$emit('refresh', data);
    },
    getThClass(item) {
      if (item.component === 'FormCheckbox') {
        return {
          'bs-checkbox': true
        };
      }
      return {};
    },
    getThStyle(item) {
      if (item.component === 'FormCheckbox') {
        return 'width:36px';
      }
      return '';
    },
    selectAll(e) {
      // 此处不能触发更新
      this.data.forEach(item => {
        item.checkbox = e.target.checked;
      });
      this.eventBus.$emit("selectAll");
      this.setCheckList();
    },
    setCheckList(){
      this.checkedList = [];
      this.data.map( item => {
        if(item.checkbox){
          this.checkedList.push(item.id);
        }
      })
      if(this.checkedList.length > 0 && this.checkedList[0] === ''){
        this.checkedList.splice(0, 1);
      }
      this.$emit("select", this.checkedList);
    },
  },
  mounted(){
    this.eventBus.$on('changeCheck', () => {
      let checked = true;
      this.data.map( item => {
        if(!item.checkbox){
          checked = false;
        }
      })
      this.checkbox = checked;
      this.setCheckList();
    })
  },
  components: {
    UiTr
  }

}

</script>

<style lang="stylus" rel="stylesheet/stylus">

</style>