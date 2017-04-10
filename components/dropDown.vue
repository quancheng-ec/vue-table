<template>
  <div style="position:relative">
    <button v-on:click="showOpts()" class="btn btn-sm btn-info btn-outline">{{config.title}}</button>
    <ul
      v-if="open"
      :style="'top:'+top"
      class="qc-dropDown-panel list-group list-group-full"
    >
      <li class="list-group-item" v-for="(opt, index) in config.opts" v-on:click="touch(index)">
        <label>{{opt.title}}</label>
        <span style="display: block; font-size: 13px;">{{opt.desc}}</span>
      </li>
    </ul>
    <div v-if="open"
          @click="open = false"
          style="z-index:9998;position:fixed;top:0;left:0;width:100%;height:100%"></div>
  </div>
</template>

<script>
  export default {
    // props: ['config'],
    data() {
      return {
        open: false,
        top: "35px"
      }
    },
    props: ["config"],
    created(){
      
    },
    methods: {
      showOpts(e){
        this.open = true;
        if (e && e.target) {
          this.isElementInViewport(e.target);
        }
      },
      touch(index){
        this.open = false;
        this.$emit("touchDrapDown", {
          index: index
        });
      },
      isElementInViewport: function (el) {
        let windowHeight = (window.innerHeight || document.documentElement.clientHeight);
        var rect = el.getBoundingClientRect();
        if (rect.bottom + 250 > windowHeight) {
          this.top = '-235px';
        }
      }
    }

  }

</script>

<style lang="stylus" rel="stylesheet/stylus">
.qc-dropDown-panel{
  z-index:9999;
  position:absolute;
  top:35px;
  border: 1px solid #e4e7ea;
  background: #fff;
  padding: 10px;
  min-width:250px;
  
  .list-group-item {
    border: 0px;
    border-top: 1px solid #ddd;
    cursor: pointer;
    label, text{
      cursor: pointer;
    }
  }

  .list-group-item:first-child {
    border: 0px;
  }

  .list-group-item:hover{
    background-color: #f5f5f5;
  }
}
</style>