<template>
  <div>
    <template v-if="item.edit">
      <button type="button"
              class="btn  btn-info btn-outline"
              @click="save()">保存</button>
      <button type="button"
              class="btn  btn-default btn-outline"
              @click="close()">取消</button>
    </template>
    <template v-else>
      <a href="javascript:;"
         @click="openEdit()"
         title="编辑"><i class="fa fa-pencil text-inverse m-r-10"></i></a>
      <a href="#"
         title="删除"
         @click="del()"><i class="fa fa-close text-danger"></i> </a>
    </template>
  </div>
</template>

<script>
export default {
  // 接收上层传递的value
  props: ['it', 'item', 'page', 'deleteAPI', 'saveAPI', 'cache'],
  data() {
    return {
    }
  },
  computed: {
    pageRequest() {
      return {
        curPage: this.page ? this.page.curPage : 1,
        pageSize: this.page ? this.page.pageSize : 15
      }
    }
  },
  methods: {
    openEdit() {
      this.item.edit = true;
      //this.$emit('edit', this.index);
    },
    close() {
      this.item.edit = false;
      if (!this.item.id) {
        this.item.hide = true;
      }
      // this.$emit('close', this.index);
    },
    del() {
      this.$confirmBox({
        content: '确认要删除' + this.item.items[0].label + '【' + this.item.items[0].value + '】这条记录么？'
      }).then(res => {
        if (res != 'confirm') {
          return;
        }
        let param = Object.assign({}, this.cache, {
          id: this.item.id
        }, this.pageRequest);
        
        console.log('del', this.cache, param);
        this.$http.post(this.deleteAPI, param).then(res => {
          this.$emit('refresh', res.data.data);
        });
      }).catch(e => { });
    },
    save() {
      let validate = true;
      this.$parent.$children.forEach(component => {
        if (component.validate && !component.validate()) {
          validate = false;
          return;
        }
      });
      if (!validate) {
        //验证不通过
        return;
      }
      console.log('save', this.cache);
      this.$http.post(this.saveAPI, Object.assign(this.cache, this.pageRequest)).then(res => {
        this.item.edit = false;
        this.cache.id = this.item.id;
        this.item.items.forEach(it => {
          if (this.cache[it.name]) {
            it.value = this.cache[it.name];
          }
        });
        this.$emit('refresh', res.data.data, this.item.id);
      });
    }

  }

}

</script>

<style lang="stylus" rel="stylesheet/stylus">

</style>