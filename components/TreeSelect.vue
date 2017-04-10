<template>
  <div class="form-group"
       style="position:relative;padding-bottom:0px;">
    <button type="button"
            @click="openSelect"
            class="btn dropdown-toggle form-control">
      <span class="filter-option pull-left text-musted">{{ selectText }}</span>&nbsp;<span class="caret"></span></button>
    <div class="qc-select-panel"
         :style="'top:'+top"
         v-if="open">
      <input type="text"
             class="form-control input-sm"
             style="width:100%"
             v-model="keyword">
      <multi-select :data='data'
                    :isSingle="isSingle"
                    :tempId="tempId"
                    @select="save"></multi-select>
      <button type="button"
              v-if="!isSingle"
              class="btn  btn-info btn-outline btn-sm"
              @click="save">确定</button>
      <button type="button"
              class="btn  btn-info btn-outline btn-sm"
              @click="open=false;">取消</button>
    </div>
    <div v-if="open"
         @click="open=false;"
         style="z-index:9998;position:fixed;top:0;left:0;width:100%;height:100%"></div>
  
  </div>
</template>

<script>

import MultiSelect from './MultiSelect.vue'
import debounce from 'lodash/debounce';

export default {
  props: ['chosenList', 'url', 'isSingle'],
  data() {
    return {
      open: false,
      keyword: '',
      tempId: '',
      top: '41px',
      data: [],
      selectText: '请选择'
    }
  },
  created() {
    if (this.index) {
      this.hierarchy = this.index;
    }
    if (this.chosenList && this.chosenList.length > 0) {
      this.selectText = '';
      this.chosenList.forEach((c, index) => {
        if (index > 0) {
          this.selectText += ',';
        }
        this.selectText += c.label;
      });
    }

  },
  watch: {
    keyword: debounce(function () {
      this.openSelect();
    },
      250
    )
  },
  mounted() {
    this.openSelect();
  },
  methods: {
    openSelect(e) {
      if (e && e.target) {
        this.isElementInViewport(e.target);
        this.open = true;
      }
      this.tempId = (new Date()).getTime();
      let cMap = {};
      this.chosenList.forEach(c => {
        cMap[c.id] = c;
      });
      //遍历数据，选中已经选择的元素
      let each = function (root, arrays, s) {
        arrays.forEach(item => {
          let status = s;
          if (cMap[item.id] || status) {
            item.selected = true;
            root.show = true;
            status = true;
          }
          if (item.children) {
            each(item, item.children, status);
          }
        });
      }
      this.$http.post(this.url, {
        keyword: this.keyword
      }).then(res => {
        //this.open = true;
        this.data = res.data.data.selectData;
        each({}, this.data, false);
      });
    },
    save() {
      const selectArray = [];
      this.data.forEach(item => {
        this.eachItem(item, selectArray);
      });
      this.open = false;

      if (selectArray.length > 0) {
        this.selectText = '';
      }

      if (this.chosenList) {
        this.chosenList.splice(0, this.chosenList.length);
      }

      selectArray.forEach((obj, index) => {
        if (index > 0) {
          this.selectText += ',';
        }
        this.selectText += obj.label;
        this.chosenList.push(obj);
      });
      //如果字符串超长，截取6个字符显示
      if (this.selectText.length > 8) {
        this.selectText = this.selectText.slice(0, 6) + '...';
      }
    },
    eachItem(item, selectArray) {
      if (item.selected) {
        selectArray.push({
          id: item.id,
          label: item.label
        });
      } else if (item.children) {
        item.children.forEach(it => {
          this.eachItem(it, selectArray);
        });
      }
    },
    isElementInViewport: function (el) {
      let windowHeight = (window.innerHeight || document.documentElement.clientHeight);
      var rect = el.getBoundingClientRect();
      if (rect.bottom + 250 > windowHeight) {
        this.top = '-235px';
      }
    }
  },
  components: {
    MultiSelect
  }

}

</script>

<style lang="stylus" rel="stylesheet/stylus">

.qc-select-panel{
  z-index:9999;
  position:absolute;
  top:41px;
  border: 1px solid #e4e7ea;
  background: #fff;
  padding: 10px;min-width:250px;
}
</style>