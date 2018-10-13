<template>
<transition name="slide-down">
  <div class="title-wrapper" v-show="isShow">
    <div class="left">
      <span class="icon-back icon"></span>
    </div>
    <div class="right">
      <div class="icon-wrapper">
        <span class="icon-cart icon"></span>
      </div>
      <div class="icon-wrapper">
        <span class="icon-person icon"></span>
      </div>
      <div class="icon-wrapper">
        <span class="icon-more icon"></span>
      </div>
    </div>
  </div>
</transition>
</template>
<script>
export default {
  props:{
    isShow:{
      type:Boolean,
      default:false
    }
  },
}
</script>
<style lang="scss">
.title-wrapper{
  display: flex;
  align-items: center;
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 48px;
  background: #eee;
  z-index: 102;
  box-shadow: 0 8px 8px rgba(0, 0, 0, 0.15);
  .left{
    flex: 0 0 60px;
    @include center;
  }
  .right{
    flex: 1;
    display: flex;
    justify-content: flex-end;
    .icon-wrapper{
      flex: 0 0 40px;
      @include center;
    }
  }

}

</style>
