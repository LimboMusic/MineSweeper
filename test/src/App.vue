<template>
  <div class="wrapper">
    <div v-for="row in state" :key='row'>
      <div 
        class="piece" 
        :class="getClass(item)"
        v-for="item in row" 
        :key="item"
        @click="handleClick(item.x,item.y)">
        <!-- {{item.x+item.y*10+1}} -->
        <div v-if="item.mine">
          <i class="iconfont icon-zhadan"></i>
        </div>
        <div v-else>
          {{item.adjacentMines}}
        </div>
        <!-- {{item.mine?'x':item.adjacentMines}} -->
      </div>
    </div>
  </div>
</template>


<script setup lang="ts">
import { reactive } from "@vue/reactivity"

interface BlockState{
  x:number
  y:number
  mine?:boolean
  adjacentMines:number
}

const HEIGHT = 10
const WIDTH = 10
//生成10X10网格
const state = reactive(Array.from({length:HEIGHT},(_,y)=>{
  return Array.from({length:WIDTH},(_,x):BlockState=>{
    return {x,
            y,
            adjacentMines:0,
    }
  })
}))

console.log(state)

//生成炸弹
function generateMines():void{
  for(const row of state){
    for(const item of row){
      item.mine = Math.random()<0.3
    }
  }
}
generateMines()


//计算非炸弹格子周围炸弹数量
const direction = [[1,0],[-1,0],[0,1],[0,-1],[-1,-1],[1,1],[1,-1],[-1,1]]
function updateNumbers(){
  state.forEach((row,y)=>{
    row.forEach((line,x)=>{
      if(line.mine) return
      direction.forEach(([dx,dy])=>{
        const x2 = x+dx
        const y2 = y+dy
        if(x2<0||x2>=WIDTH||y2<0||y2>=HEIGHT) return
        if(state[y2][x2].mine) line.adjacentMines++
      })
    })
  })
}
updateNumbers()


function getClass(item:BlockState){
  return item.mine?'state-red':'state-gray'
}

function handleClick(x:number,y:number):void{
  console.log(x,y)
}
</script>


<style scoped>
@import url(//at.alicdn.com/t/c/font_3634439_mg0ehtr0pyf.css);
/* .logo {
  height: 6em;
  padding: 1.5em;
  will-change: filter;
}
.logo:hover {
  filter: drop-shadow(0 0 2em #646cffaa);
}
.logo.vue:hover {
  filter: drop-shadow(0 0 2em #42b883aa);
} */
*{
  box-sizing: border-box;
}
.piece{
  /* display: inline-block; */
  background-color: red;
  color: #fff;
  width: 30px;
  height: 30px;
  border:1px solid black;
  cursor: pointer;
  transition: 0.15s;
}

.piece:hover{
  background-color: green;
}

.state-red{
  background-color: red;
}

.state-gray{
  background-color: gray;
}
</style>
