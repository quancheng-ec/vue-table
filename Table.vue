<template>
  <div >
    <table class="table">
      <thead>
        <tr>
          <th v-for="item in data[0].items" v-if="!item.hidden" :class="getThClass(item)" :style="getThStyle(item)">
            <template v-if="item.component==='FormCheckbox'">
              <div class="th-inner ">
                <input name="btSelectAll" type="checkbox" @click="selectAll">
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
        <ui-tr v-for="(item,index) in data" :index="index" :item='item' :page="page" v-if='!item.hide' :saveAPI="saveAPI" :deleteAPI="deleteAPI" @refresh='onRefresh(arguments[0])'></ui-tr>
      </tbody>
    </table>
  </div>
</template>

<script>
import UiTr from './components/Tr.vue'

export default {
  props: ['data', 'page', 'deleteAPI', 'saveAPI'],
  data() {
    return {
    }
  },
  created() {
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
      this.data.forEach(item => {
        item.checkbox = e.target.checked;
      });
    }
  },
  components: {
    UiTr
  }

}

</script>

<style lang="stylus" rel="stylesheet/stylus">

</style>