<template>
  <!-- 分页 -->
  <div class="row">
    <form class="form-inline" v-if="page.num>0 && totalPage>1">
      <div class="col-md-4">
        <div class="form-group ">
          <label class="control-label"
                 style="margin:9px 0px;">共{{page.total}}条记录,当前显示{{ transformShowNum() }}条</label>
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
          <label class="control-label">共{{totalPage}}页,</label>
          <label class="control-label">当前第</label>
          <input type="number"
                 class="form-control "
                 min="1"
                 :max="totalPage"
                 style="width: 50px;"
                 v-model="curPage">
          <label class="control-label">页</label>
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
      暂无记录,请添加记录或者导入
    </div>
  </div>
  <!-- 分页 -->
</template>

<script>
export default {
  props: ['pageSize', 'url', 'query', 'page'],
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
    this.limit = this.pageSize ? this.pageSize : 10;
    this.curPage = this.page.curPage;
  },
  computed: {
    totalPage: function () {
      let page = (this.page.total + this.limit - 1) / this.limit;
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
    loadPage() {
      this.$http.post(this.url, {
        curPage: this.page.curPage,
        pageSize: this.pageSize,
        total: this.page.total,
        query: this.query || {}
      }).then(res => {
        this.page.items = res.data.data.items;
        this.curPage = this.page.curPage;
        this.page.num = res.data.data.num;
        // if (res.data.data.total) {
        this.page.total = res.data.data.total;
        if (this.$toast) {
          this.$toast('加载成功!');
        }
        //}
        //this.transformShowNum();
        this.$emit('pageRefresh',res.data.data);
      });
    }
  }
}

</script>

<style lang="stylus" rel="stylesheet/stylus">

</style>