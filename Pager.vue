<template>
  <!-- 分页 -->
  <div class="row">
    <form class="form-inline">
      <div class="col-md-4">
        <div class="form-group ">
          <label class="control-label"
                 style="margin:9px 0px;">{{i18n('leftHint')}}</label>
          <label class="control-label"> {{i18n('perPageLeft')}}</label>
          <select name=""
                  class="form-control"
                  :value="limit"
                  @change="loadPage($event.target.value)">
            <option :value="pageSize"
                    v-if="pageSize">{{pageSize}}</option>
            <option value="20">20</option>
            <option value="50">50</option>
            <option value="100">100</option>
            <option value="200">200</option>
          </select>
          <label class="control-label"> {{i18n('perPageRight')}}</label>
        </div>
      </div>
      <div class="col-md-8 text-right">
        <div class="form-group">
          <button type="button"
                  class="btn  btn-info btn-outline"
                  @click="changePage('first')">«</button>
          <button type="button"
                  class="btn  btn-info btn-outline"
                  @click="changePage('prev')">‹</button>
          <label class="control-label">{{i18n('total')}}</label>
          <input type="number"
                 class="form-control "
                 min="1"
                 :max="totalPage"
                 style="width: 50px;"
                 v-model="curPage">
          <label class="control-label">{{i18n('page')}}</label>
          <button type="button"
                  class="btn  btn-info btn-outline"
                  @click="changePage('next')">›</button>
          <button type="button"
                  class="btn  btn-info btn-outline"
                  @click="changePage('last')">»</button>
  
        </div>
      </div>
    </form>
    <div v-if="page.num<=0"
         style="text-align:center;">
      {{i18n('emoty')}}
    </div>
  </div>
  <!-- 分页 -->
</template>

<script>
export default {
  props: ['pageSize', 'url', 'query', 'page', 'language'],
  data() {
    return {
      curPage: 1,
      //当前页数,从1开始
      limit: 0,//记录每页数，为了避免vue组件参数传递影响，单独存储一下
      items: []
    }
  },
  created() {
    //如果组件调用方 pageSize没有设置，给个默认值
    this.limit = this.pageSize || 10;
    this.curPage = this.page.curPage;
  },
  computed: {
    totalPage: function () {
      let page = Math.ceil(this.page.total / this.limit)
      return parseInt(page);
    }
  },
  watch: {
    // 监听 页数的变化，做分页加载
    curPage: function () {
      let curPage = parseInt(this.curPage);
      if (curPage > this.totalPage) {
        this.curPage = this.totalPage;
      }
      if (this.page.curPage != curPage) {
        this.page.curPage = curPage;
        this.loadPage();
      }
    },
    // 监听query变化，如果有变化则重新加载页面
    query: {
      handler: function () {
        this.loadPage();
      },
      deep: true
    }
  },
  methods: {
    i18n(key) {
      if (key == 'leftHint') {
        if (this.language == 'en') {
          return "Records " + this.transformShowNum() + ' of ' + this.page.total;
        }
        return "共" + this.page.total + "条记录,当前显示" + this.transformShowNum() + "条";
      } else if (key == 'total') {
        if (this.language == 'en') {
          return "Total " + this.totalPage + " page, jump to "
        }
        return "共" + this.totalPage + "页,当前第"
      } else if (key == 'page') {
        if (this.language == 'en') {
          return " page ";
        } else {
          return "页";
        }
      } else if (key == 'empty') {
        if (this.language == 'en') {
          return 'Empty';
        } else {
          return '暂无信息'
        }
      } else if (key == 'perPageLeft') {
        if (this.language == 'en') {
          return '';
        } else {
          return '每页显示'
        }
      } else if (key == 'perPageRight') {
        if (this.language == 'en') {
          return 'per page';
        } else {
          return ''
        }
      }
    },
    changePage(status) {
      if (status === 'first') {
        this.curPage = 1;
      }
      if (status === 'prev') {
        if (this.curPage > 1) {
          this.curPage -= 1;
        }
      }
      if (status === 'next') {
        if (this.curPage < this.totalPage) {
          this.curPage += 1;
        }
      }
      if (status === 'last') {
        this.curPage = this.totalPage;
      }
    },
    // 显示 记录数
    transformShowNum() {
      let start = (this.page.curPage - 1) * this.limit + 1;
      let end = start + this.page.num - 1;
      return start + "~" + end;
    },
    //分页查询
    loadPage(pageSize) {
      this.$http.post(this.url, {
        curPage: this.page.curPage,
        pageSize: Number(pageSize) || this.limit,
        total: this.page.total,
        query: this.query || {}
      }).then(res => {
        this.page.items = res.data.data.items;
        this.curPage = this.page.curPage;
        this.page.num = res.data.data.num;
        this.limit = res.data.data.pageSize;
        // if (res.data.data.total) {
        this.page.total = res.data.data.total;
        if (this.$toast) {
          this.$toast(this.language == 'en' ? 'Loaded Successfully' : '加载成功!');
        }
        //}
        //this.transformShowNum();
        this.$emit('pageRefresh', res.data.data);
      });
    }
  }
}

</script>

<style lang="stylus" rel="stylesheet/stylus">

</style>