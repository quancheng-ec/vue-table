<template>
  <tr>
    <td v-for="it in item.items"
        v-if="!it.hidden">
      <!-- 如果有编辑组件，当数据状态为编辑态时，切换到编辑组件 -->
      <template v-if="item.edit && it.editComponent">
        <component :is="it.editComponent"
                   :item="item"
                   :it="it"
                   :index="index"
                   :value="it.value"
                   @input="cache[it.name] = arguments[0]"
                   @refresh="onRefresh(arguments[0])"
                   :cache='cache'></component>
      </template>
      <template v-else>
        <!-- 默认显示文本，如果有自定义展示，展示自定义组件 -->
        <template v-if="it.component">
          <component :is="it.component"
                     :item="item"
                     :it="it"
                     :index="index"
                     :page='page'
                     :deleteAPI='deleteAPI'
                     :saveAPI='saveAPI'
                     :cache='cache'
                     @refresh="onRefresh(arguments[0])"></component>
        </template>
        <template v-else>
          {{it.value}}
        </template>
      </template>
    </td>
  
  </tr>
</template>

<script>
import FormInput from './Input.vue';
import FormCheckbox from './Checkbox.vue';
import FormA from './A.vue';
import FormImageText from './ImageText.vue';

import FormAction from './Action.vue';
import FormTreeSelect from './TreeSelect.vue';



export default {
  props: ['item', 'index', 'page', 'deleteAPI', 'saveAPI'],
  data() {
    return {
      cache: {}
    }
  },
  created() {
    this.init();
  },
  watch: {
    item() {
      this.init();
    }
  },
  methods: {
    init() {
      this.cache.id = this.item.id;
      this.item.items.forEach(item => {
        if (item.name) {
          this.cache[item.name] = item.hidden ? item.value : '';
        }
      });
    },
    onRefresh(data) {
      this.$emit('refresh', data);
    }
  },
  components: {
    FormInput, FormCheckbox, FormA, FormAction, FormTreeSelect, FormImageText
  }
}

</script>

<style lang="stylus" rel="stylesheet/stylus">

</style>
