<template>
  <div class="ebook">
    <title-bar :isShow="isShow"></title-bar>
    <div class="read-wrapper">
      <div id="read"></div>
      <div class="mask">
        <div class="left" @click="prevPage"></div>
        <div class="center" @click="toggleShow"></div>
        <div class="right" @click="nextPage"></div>
      </div>
    </div>
    <menu-bar
      :isShow="isShow"
      ref="menubar"
      @setFontSize="setFontSize"
      :themeList="themeList"
      :defaultTheme="defaultTheme"
      @setTheme="setTheme"
      @onProgressChange="onProgressChange"
      :bookAvailable="bookAvailable"
      :navigation="navigation"
      @jumpTo="jumpTo"
      ></menu-bar>
  </div>
</template>
<script>
  import TitleBar from './TitleBar'
  import MenuBar from './MenuBar'
  import Epub from 'epubjs'
  export default {
    data(){
      return {
        DOWNLOAD_URL:'/static/2018_Book_AgileProcessesInSoftwareEngine.epub',
        // book:{},
        autoPlays:false,
        isShow:false,
        themeList:[
          {
            name:'default',
            style:{
              body:{
                'color':'#000',
                'background':'#fff'
              }
            }
          },
          {
            name:'eye',
            style:{
              body:{
                'color':'#000',
                'background':'#ceeaba'
              }
            }
          },
          {
            name:'night',
            style:{
              body:{
                'color':'#dedede',
                'background':'#333'
              }
            }
          },
          {
            name:'gold',
            style:{
              body:{
                'color':'#000',
                'background':'rgb(241,236,226)'
              }
            }
          },
        ],
        defaultTheme:0,
        // 当前书可用状态
        bookAvailable:false,
        navigation: {}
      }
    },
    components:{
      TitleBar,
      MenuBar
    },
    methods:{
      // 电子书的解析和渲染
      showEpub(){
        // 生成book
        this.book=new Epub(this.DOWNLOAD_URL);
        // 生成Rendition 通过Book.renderTo
        this.rendition=this.book.renderTo('read',{
          width:window.innerWidth,
          height:window.innerHeight
        })
        // 渲染电子书
        this.rendition.display();
        // 获取theme对象
        this.themes=this.rendition.themes;
        // 设置主题对象
        this.registTheme();
        this.setTheme(this.defaultTheme);
        // 设置进度条
        this.book.ready.then(()=>{
          this.navigation=this.book.navigation;
          return this.book.locations.generate();
        }).then(result=>{
          this.locations=this.book.locations;
          this.bookAvailable=true;
          // this.onProgressChange(50);
        })
      },
      prevPage(){
        if (this.rendition) {
          this.rendition.prev()
        }
      },
      nextPage(){
        if (this.rendition) {
          this.rendition.next()
        }
      },
      autoPlay(){
        if (this.rendition) {
          this.autoPlays=!this.autoPlays;
          if (this.autoPlays) {
            this.timer=setInterval(()=>{
              this.rendition.next()
            },3000)
          }else {
            clearInterval(this.timer)
          }
          this.toggleShow();
        }
      },
      toggleShow(){
        this.isShow=!this.isShow;
        if (!this.isShow) {
          this.$refs.menubar.hideSetting();
        }
      },
      setFontSize(val){
        // 获取theme对象
        if (this.themes) {
          this.themes.fontSize(val+'px');
        }
      },
      registTheme(){
        this.themeList.forEach(theme=>{
          this.themes.register(theme.name,theme.style)
        })
      },
      setTheme(index){
        this.themes.select(this.themeList[index].name);
        this.defaultTheme=index;
      },
      onProgressChange(progress){
        const percentage = progress/100;
        const location = percentage > 0 ?
            this.locations.cfiFromPercentage(percentage) : 0;
        this.rendition.display(location)
      },
      // 目录跳转
      jumpTo(href){
        this.rendition.display(href);
        this.hideTitleAndMenu();
      },
      hideTitleAndMenu(){

        // 隐藏标题栏和菜单栏
        this.isShow = false;
        // 隐藏菜单栏弹出的设置栏
        this.$refs.menubar.hideSetting();
        // 隐藏目录
        this.$refs.menubar.hideContent();
      }
    },
    mounted(){
      this.showEpub();
    }
  }
</script>
<style lang="scss">
  .ebook{
    position: relative;
    .read-wrapper{
      .mask{
        position: absolute;
        top: 0;
        left: 0;
        width: 100%;
        height: 100%;
        z-index: 100;
        display: flex;
        .left{
          flex: 0 0 px2rem(100);
        }
        .center{
          flex:1;
        }
        .right{
          flex: 0 0 px2rem(100);
        }
      }
    }

  }
</style>
