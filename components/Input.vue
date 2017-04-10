<template>
  <div :class="{'form-group':true, 'has-error': isError}">
    <input type="text" class="form-control" :value="val" @input="update($event.target.value)" :autofocus="autofocus"/>
    <span class="help-block"> {{error}} </span>
  </div>
</template>

<script>
  export default {
    // 接收上层传递的value
    props: [ 'value','autofocus'],
    data() {
      return {
        val:'',//缓存input的真实值
        error:'',
        isError: false
      }
    },
    created(){
      if(this.value){
        this.val = this.value;
        this.$emit('input',this.value);
      }
    },
    watch: {
      error: function () {
        if (this.error) {
          this.isError = true;
        } else {
          this.isError = false;
        }
      },
      val:function(){
        if(this.val){
          this.error = "";
        }
      }
    },
    methods: {
      update(value) {
        this.val = value;
        this.$emit('input',value);
      },
      validate(){
        if(!this.val){
          this.error = '内容不能为空';
          return false;
        }
        return true;
      }
    }

  }

</script>

<style lang="stylus" rel="stylesheet/stylus">

</style>