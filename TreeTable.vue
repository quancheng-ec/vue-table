<template>
  <div>
    <table class="table">
      <thead>
        <tr>
          <th v-for="item in columns"
              :width="item.width"
              v-if="!item.hidden"
              :class="getThClass(item)"
              :style="getThStyle(item)">
            <template v-if="item.component==='FormCheckbox'">
              <div class="th-inner ">
                <input name="btSelectAll"
                       type="checkbox"
                       @click="selectAll">
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
        <template v-for="(item,index) in list">
          <tr v-show='item.$expand'>
            <td v-for="(it,i) in columns"
                v-if="!it.hidden"
                :style="computedLeft(item,i)">
              <!-- 默认显示文本，如果有自定义展示，展示自定义组件 -->
              <template v-if="it.component">
                <a href="javascript:;"
                   @click="toexpand(item)"
                   v-if="i===0&&((item.children&&item.children.length>0) || item.$level)"
                   style="margin-right:5px;"><i :class="item.$style"></i></a>
                <component :is="it.component"
                           :item="item"
                           :it="it"
                           :index="index"
                           :page='page'></component>
              </template>
              <template v-else>
                <a href="javascript:;"
                   @click="toexpand(item)"
                   v-if="i===0&&((item.children&&item.children.length>0) || item.$level)"
                   style="margin-right:5px;"><i :class="item.$style"></i></a> {{item[it.name]}}
              </template>
            </td>
          </tr>
        </template>
      </tbody>
    </table>
  </div>
</template>

<script>
import UiTr from './components/Tr.vue'
import T_A from './libs/A.vue';
import T_Switcher from './libs/Switcher.vue';
import T_Input from './libs/Input.vue';
import T_Select from './libs/Select.vue';


export default {
  props: ['page', 'columns', 'expand'],
  data() {
    return {
      list: []
    }
  },
  created() {
    this.init();
  },
  watch: {
    page: {
      handler: function () {
        this.init();
      },
      deep: true
    },
    list: {
      handler: function () {
        this.$emit('update', this.list);
      },
      deep: true
    }
  },
  methods: {
    init() {
      if (!this.page) {
        return;
      }
      this.list = [];
      this.page.items.forEach(d => {
        this.deepConvert(d, 0);
      });
    },
    deepConvert(item, level) {
      item.$level = level;
      if (this.expand && this.expand === 'false') {
        item.$expand = false;
        item.$style = item.children && item.children.length > 0 ? "fa fa-caret-right" : "";
      } else {
        item.$expand = true;
        item.$style = item.children && item.children.length > 0 ? "fa fa-caret-down" : "";
      }

      // if (level > 0) {
      //   item.$expand = false;
      // }
      let result = Object.assign({}, item, { children: [] });
      this.list.push(result);
      if (item.children) {
        item.children.forEach(it => {
          result.children.push(this.deepConvert(it, level + 1));
        })
      }
      return result;

    },
    toexpand(item, close) {
      if (close) {
        item.$expand = !item.$expand;
      }
      if (item.children && item.children.length > 0) {
        console.log(item);
        item.$style = item.$style !== 'fa fa-caret-down' ? "fa fa-caret-down" : "fa fa-caret-right";
        item.children.forEach(it => {
          this.toexpand(it, true);
        })
      }

    },
    computedLeft(item, i) {
      if (i > 0) {
        return '';
      }
      return 'padding-left:' + (item.$level * 15 + 8) + 'px';
    },
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
    T_A, T_Switcher,T_Input,T_Select
  }

}

</script>

<style lang="stylus" rel="stylesheet/stylus">

</style>