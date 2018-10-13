<template>
<div class="menu-bar">
  <transition name="slide-up">
    <div class="menu-wrapper" :class="{'hide-box-shadow':isSetting}" v-show="isShow">
      <div class="icon-wrapper" @click="showSetting(3)">
        <span class="icon icon-menu"></span>
      </div>
      <div class="icon-wrapper" @click="showSetting(2)">
        <span class="icon icon-progress"></span>
      </div>
      <div class="icon-wrapper" @click="showSetting(1)">
        <span class="icon icon-bright"></span>
      </div>
      <div class="icon-wrapper" @click="showSetting(0)">
        <span class="icon icon-a"></span>
      </div>
    </div>
  </transition>

  <div class="setting-wrapper" v-show='isSetting'>
    <div class="setting-font" v-if="showTag===0">
      <div class="icon-wrapper">
        <span class="icon icon-a icon-a-first"></span>
      </div>
      <el-slider
        class="menu-slider"
        v-model="fontSize"
        :min="10"
        :max="20"
        @change="setFont">
      </el-slider>
      <div class="icon-wrapper">
        <span class="icon icon-a"></span>
      </div>
    </div>
    <div class="setting-theme" v-else-if="showTag===1">
      <div
        class="setting-theme-item"
        v-for="(item,index) in themeList"
        :key='index'
        @click="changeTheme(index)">
        <div class="preview" :style="{'background':item.style.body.background}" :class="{'has-border':item.style.body.background==='#fff'}"></div>
        <div class="text" :class="{'selected':index===defaultTheme}">{{item.name}}</div>
      </div>
    </div>
    <div class="setting-progress" v-else-if="showTag===2">
      <div class="progress-wrapper">
        <el-slider
          class="progress-slider"
          v-model="progress"
          :min="0"
          :max="100"
          @change="setProgress">
        </el-slider>
      </div>
    </div>
  </div>

  <content-view :ifShowContent="ifShowContent"
                  v-show="ifShowContent"
                  :navigation="navigation"
                  :bookAvailable="bookAvailable"
                  @jumpTo="jumpTo"></content-view>
  <transition name="fade">
    <div class="content-mask"
          v-show="ifShowContent"
          @click="hideContent"></div>
  </transition>
</div>
</template>
<script>
import ContentView from './Content'
export default {
  props:{
    isShow:{
      type:Boolean,
      default:false
    },
    themeList:Array,
    defaultTheme:Number,
    bookAvailable:{
      type:Boolean,
      default:false
    },
    navigation:Object
  },
  data(){
    return{
      fontSize:14,
      isSetting:false,
      showTag:0,
      progress:10,
      ifShowContent: false
    }
  },
  components:{
    ContentView
  },
  methods:{
    showSetting(index){
      this.showTag=index;
      if (this.showTag === 3) {
        this.isSetting = false;
        this.ifShowContent = true;
      } else {
        this.isSetting = true;
      }
    },
    hideSetting(){
      this.isSetting=false;
    },
    setFont(size){
      this.$emit('setFontSize',size);
    },
    changeTheme(index){
      this.$emit('setTheme',index)
    },
    setProgress(percentage){
      this.$emit('onProgressChange',percentage)
    },
    // 隐藏目录
    hideContent() {
      this.ifShowContent = false;
    },
    // 跳转方法，调用父组件方法
    jumpTo(target) {
      this.$emit('jumpTo', target)
    },
  }
}
</script>
<style lang="scss">
.menu-bar{
  .menu-wrapper{
    display: flex;
    align-items: center;
    position: absolute;
    bottom: 0;
    left: 0;
    width: 100%;
    height: 48px;
    background: #eee;
    z-index: 102;
    box-shadow: 0 -8px 8px rgba(0, 0, 0, 0.15);
    &.hide-box-shadow{
      box-shadow: none;
    }
    .icon-wrapper{
      flex: 1;
      height: 100%;
      cursor: pointer;
      @include center;
    }
  }

  .setting-wrapper{
    position: absolute;
    left: 0;
    bottom:48px;
    z-index: 102;
    width: 100%;
    height: 50px;
    box-shadow: 0 -8px 8px rgba(0,0,0,.15);
    background: #efefef;
    .setting-font{
      height: 100%;
      border-bottom: 1px solid #dedede;
      @include center;
      .menu-slider{
        width: 60%;
      }
      .icon-wrapper{
        flex: 1;
        @include center;
        .icon-a-first{
          font-size:10px
        }
      }
    }
    .setting-theme{
      height: 100%;
      display: flex;
      .setting-theme-item{
        padding:px2rem(20);
        flex: 1;
        display: flex;
        flex-direction: column;
        align-items: center;
        .preview{
          width: 100%;
          flex: 1;
          border-radius: px2rem(5);
          &.has-border{
            border:1px solid #ccc;
          }
        }
        .text{
          margin-top: px2rem(5);
          flex: 0 0 px2rem(20);
          font-size: px2rem(14);
          color: #444;
          &.selected{
            color: #333;
            font-weight: 500;
          }
        }
      }
    }
    .setting-progress{
      height: 100%;
      display: flex;
      .progress-wrapper{
        flex:1;
        @include center;
        .progress-slider{
          width: 90%;
          justify-content: center;
          align-items: center;
        }
      }
    }
  }
  .content-mask {
    position: absolute;
    top: 0;
    left: 0;
    z-index: 108;
    display: flex;
    width: 100%;
    height: 100%;
    background: rgba(51, 51, 51, .8);
  }

}
</style>
