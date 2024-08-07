<template>
  <div class="app">
    <audio  @timeupdate="setOffset" ref="audioRef" class="audioCss" controls="true" src="src\assets\music.mp3"></audio>
    <div class="wordContent">
      <ul ref="ulRef">
        <li class="word" v-for="(item,index) in data" :key="index" :class="{active:index===activeIndex}">{{item.word}}</li>
      </ul>
    </div>
  </div>
</template>

<script setup>
import axios from 'axios';
import { onMounted } from 'vue';
import { ref } from 'vue';
const data=ref([])
const activeIndex=ref(0)
const audioRef=ref(null)
const ulRef=ref(null)
const getData=async()=>{
   axios.get('/data.json').then((res)=>{
     data.value=res.data.data
     data.value.forEach((item)=>{
     item.time=parseTime(item.time)
   })
   })
} 
const parseTime = (timeStr) => {
  let timeArr = timeStr.split(':'); 
  return parseInt(timeArr[0]) * 60 + parseFloat(timeArr[1]);
};
// 获取当前播放时间和歌词
const findCurrentIndex = () => {
  for(let i=0;i<data.value.length;i++){
    if(data.value[i].time<=audioRef.value.currentTime){
      activeIndex.value=i
      
    }
  }
}
// 容器高度
const containerHeight=420
// li高度
const liHeight=30

const setOffset=()=>{
  findCurrentIndex()
  // 获取dom得在交互中
  const maxOffset = ulRef.value.clientHeight;    
   let offset=liHeight*activeIndex.value+liHeight/2-containerHeight/2 
   if(offset<0){
     offset=0
   }
   else if(offset>maxOffset){
     offset=maxOffset
   }
   else{
   ulRef.value.style.transform=`translateY(-${offset}px)`
   }
}

onMounted(async()=>{
 await getData()

})
</script>

<style >
/*样式li不能存在margin 不然会出现距离的动画错误 */
.app{
  background-color: black;
  text-align: center;
  height: 100vh;

}
.audioCss{
  margin: 50px 0;
  width: 500px;
  }
  .wordContent{

    height: 420px;
    text-align: center;
    overflow: hidden;
  }
  .wordContent ul{
    list-style: none;
    padding: 0;
    margin: 0;
    transition: all 0.5s;
  }
  .word{
    display: block;
    height: 30px;
    line-height: 30px;
    color: rgb(208, 202, 202);
    transition: all 0.5s;
  }
  .active{
    color: white;
    transform: scale(1.4);
  }
</style>