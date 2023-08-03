<!--  -->
<template>
  <div
    ref='scrollContainerEl'
    class="scroll-wrap"
    @mouseenter='onMouseEnter'
    @mouseleave='onMouseLeave'
    :style="{
      width: props.width,
      height: props.height
    }"
    :class="{
      'vertical-mode':props.mode === 'horizontal'
    }"
  >
    <div class="scroll-container" ref='scrollWrapEl'>
      <slot></slot>
    </div>
    <div class="scroll-container" v-if='scrolling'>
      <slot></slot>
    </div>
  </div>
</template>

<script lang='ts' setup>
    import { onUnmounted,ref,onMounted,nextTick, defineProps, computed } from 'vue'
    const props = defineProps({
      width:{
        type:String,
        default:'100%',
      },
      height:{
        type:String,
        default:'100%',
      },
      mode: {
        type:String,
        default:'vertical', // vertical | horizontal
      },
      step:{
        type:Number,
        default:1, // 每次移动像素量
      }
    })
    const scrollWrapEl = ref();
    const scrollContainerEl = ref()
    let scrolling = ref(false);
    let mouseenter = false;
    let aniFrameFlag;
    let resizeObserver;
    const curModeRectProp = computed(()=>{
      if(props.mode === 'vertical'){
        return ['scrollHeight','scrollTop','clientHeight']
      }else{
        return ['scrollWidth','scrollLeft','clientWidth']
      }
    })
    function doAutoScroll(){
      if(!mouseenter){
        let {
          [curModeRectProp.value[0]]:sHeight,
          [curModeRectProp.value[1]]:sTop, 
          [curModeRectProp.value[2]]:cHeight
        } = scrollContainerEl.value
        scrollContainerEl.value[curModeRectProp.value[1]] += props.step;
        // 没有滚动条了
        if(sHeight / 2 <= cHeight){
          scrolling.value = false;
          return
        }
        if(parseInt(sHeight / 2) <=  parseInt(sTop)){
          scrollContainerEl.value[curModeRectProp.value[1]] = 0;        
        }
      }
      aniFrameFlag = requestAnimationFrame(doAutoScroll)
    }
    function doResizeObserver(){
      resizeObserver =  new ResizeObserver((entries) => {
        for (const entry of entries) {
          let {
            height,
            width
          } = entry.contentRect;
          let { [curModeRectProp.value[2]]:cHeight } = scrollContainerEl.value;
          let directionSize = props.mode === 'vertical' ? height : width;
          if(directionSize > cHeight){
            if(scrolling.value){
              return
            }else{
              scrolling.value = true
            }
            nextTick(()=>{
              doAutoScroll()
            })
          }
        }
      });
    }
    doResizeObserver()

    function onMouseEnter(){
      mouseenter = true;
    }
    function onMouseLeave(){
      mouseenter = false;
    }
    onMounted(()=>{
      resizeObserver.observe(scrollWrapEl.value);
    })
    onUnmounted(()=>{
      resizeObserver.disconnect()
      cancelAnimationFrame(aniFrameFlag)
    })
</script>
<style lang='scss' scoped>
.scroll-wrap{
  overflow: auto;
}
.scroll-wrap::-webkit-scrollbar{
  display: none;
}
.vertical-mode{
  display: flex;
  justify-content: flex-start;
  align-items: flex-start;
}
.vertical-mode .scroll-container{
  flex-shrink: 0;
  display: flex;
  justify-content: flex-start;
}

</style>