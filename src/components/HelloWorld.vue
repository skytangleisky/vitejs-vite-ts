<script setup lang="ts">
/*
外星钟

“联圣星”是一颗距离银河系最近一个星系里的行星， 以下是地球与联圣星的时间信息: 1.地球与联圣星的时间差别：
 地球时间 1970-1-1, 12:00:00am 相等于联圣星的 2804 年, 18 月, 31 号, 上午 2 时, 2 分, 88 秒。
 联圣星的一秒等于地球的 0.5 秒。

2.联圣星时间细节：
1 分钟有 90 秒。
1 小时有 90 分钟。
1 天有 36 小时。
1 年有 18 个月。
 每个月的天数分布：
月 1  2  3  4   5  6  7  8  9
日 44 42 48 40 48 44 40 44 42
月 10 11 12 13 14 15 16 17 18
日 40 40 42 44 48 42 40 44 38

使用 Vue，Vuex，Typescript，Javascript，HTML，CSS/SCSS/Tailwind 编译一个独立的网页程序，添加一下功能：
 显示联圣星的时钟（创意加分），时钟需要显示当前的日期与时间，时间必须以联圣星秒速更新。
 允许用户调整日期与时间，必须检查用户输入值是否正确。

加分:
 基于联圣星的时间显示对应的地球时间。
 允许设置闹钟。
 响应式设计 （Responsive Design）
*/
import { ref,onMounted,onUnmounted } from 'vue'
import { ElMessage } from 'element-plus'
defineProps<{ msg: string }>()

const count = ref(0)
let daysPerMonthArr: Array<Number> = [44,42,48,40,48,44,40,44,42,40,40,42,44,48,42,40,44,38]
/*
当“联圣星”时间为2805-1-1 00:00:00年时，和其时间为2804 年, 18 月, 31 号, 上午 2 时, 2 分, 88 秒，经过了（2+87*90+90*90*33 + 6*90*90*36+90*90*36）“联圣星”秒
let year=2804
let month=18
let day=31
let hour=2
let minute=2
let second=88
for(let i=0;i<2+87*90+90*90*33 + 6*90*90*36+90*90*36;i++){
  if(++second===90){
    second=0
    if(++minute===90){
      minute=0
      if(++hour===36){
        hour=0
        if(++day>daysPerMonthArr[month-1]){
          day=1
          if(++month>18){
            month=1
            year++
          }
        }
      }
    }
  }
}
console.log(year+'-'+month+'-'+day+' '+hour+':'+minute+':'+second)
*/
let timeString:String=ref("0000-00-00 00:00:00")
let timeStringEarth:String=ref("0000-00-00 00:00:00")
let inputTimeString:[String,undefied]=ref("2819-17-22 10:57:34")
let timer:Integer=0
let seconds:[Number,undefied] 
onMounted(()=>{
  // seconds = Math.floor(Date.now()*2/1000) - (2+87*90+90*90*33 + 7*90*90*36) + 2805*eval(daysPerMonthArr.join('+'))*36*90*90
  let time = {
    year:2804,
    month:18,
    day:31,
    hour:2,
    minute:2,
    second:88
  }
  seconds = time.year*eval(daysPerMonthArr.join('+'))*36*90*90+(time.month>1?eval(daysPerMonthArr.filter((v,k,arr)=>k<time.month-1).join('+'))*36*90*90:0)+(time.day-1)*36*90*90+time.hour*90*90+time.minute*90+time.second + Math.floor(Date.now()*2/1000)
  proc(seconds)
  timer = setInterval(()=>{
    proc(++seconds)
    let timeEarth = new Date((seconds-(time.year*eval(daysPerMonthArr.join('+'))*36*90*90+(time.month>1?eval(daysPerMonthArr.filter((v,k,arr)=>k<time.month-1).join('+'))*36*90*90:0)+(time.day-1)*36*90*90+time.hour*90*90+time.minute*90+time.second))/2*1000)
    timeStringEarth.value = `${timeEarth.getFullYear().toString().padStart(4,'0')}-${(timeEarth.getMonth()+1).toString().padStart(2,'0')}-${timeEarth.getDate().toString().padStart(2,'0')} ${timeEarth.getHours().toString().padStart(2,'0')}:${timeEarth.getMinutes().toString().padStart(2,'0')}:${timeEarth.getSeconds().toString().padStart(2,'0')}`
  },500)
})
function proc(seconds){
  let y:Number = Math.floor(seconds/(eval(daysPerMonthArr.join('+'))*36*90*90))
  let M:Number = daysPerMonthArr.filter((v,k,arr)=>{
    let sum:Number = 0;
    for(let i=0;i<k;i++){
      sum+=arr[i]
    }
    return sum<=Math.floor((seconds%(eval(daysPerMonthArr.join('+'))*36*90*90))/(36*90*90))
  }).length
  let d:Number = M>1?Math.floor((seconds%(eval(daysPerMonthArr.join('+'))*36*90*90))/(36*90*90)) - eval(daysPerMonthArr.filter((v,k,arr)=>k<M-1).join('+')):Math.floor((seconds%(eval(daysPerMonthArr.join('+'))*36*90*90))/(36*90*90))+1

  let h:Number = Math.floor((seconds%(36*90*90))/(90*90))
  let m:Number = Math.floor((seconds%(90*90))/90)
  let s:Number = Math.floor(seconds%90)
  timeString.value = `${y.toString().padStart(4,'0')}-${M.toString().padStart(2,'0')}-${d.toString().padStart(2,'0')} ${h.toString().padStart(2,'0')}:${m.toString().padStart(2,'0')}:${s.toString().padStart(2,'0')}`
  // console.log(timeString.value)
}
function parseTime(params:string) {
  if(new RegExp(/^[0-9]{4}-[0-9]{2}-[0-9]{2} [0-9]{2}:[0-9]{2}:[0-9]{2}$/g).test(params)){
    let year = Number(params.substr(0,4))
    let month = Number(params.substr(5,2))
    let day = Number(params.substr(8,2))
    let hour = Number(params.substr(11,2))
    let minute = Number(params.substr(14,2))
    let second = Number(params.substr(17,2))
    if(month==0||month>18){
      return false
    }
    if(day==0||day>daysPerMonthArr[month-1]){
      return false
    }
    if(hour>=36||minute>=90||second>=90){
      return false
    }
    return {year,month,day,hour,minute,second}
  }else{
    return false
  }
}
// console.log(parseTime('2819-17-22 08:02:85'))

onUnmounted(()=>{
  timer&&clearInterval(timer)
})
let confirm = (val) => {
  let time = parseTime(val)
  if(time){
    seconds = time.year*eval(daysPerMonthArr.join('+'))*36*90*90+(time.month>1?eval(daysPerMonthArr.filter((v,k,arr)=>k<time.month-1).join('+'))*36*90*90:0)+(time.day-1)*36*90*90+time.hour*90*90+time.minute*90+time.second
    proc(seconds)
  }else{
    ElMessage({
      message: '格式错误！',
      type: 'error',
    })
  }
}

</script>

<template>
  <!-- <h1>{{ msg }}</h1> -->
  <div id="clock">
    <div class="time" v-text="timeString"></div>
    <el-input v-model="inputTimeString" :placeholder="timeString"></el-input>
    <el-button @click="confirm(inputTimeString)">确定</el-button>
    <div class="time" v-text="timeStringEarth"></div>
  </div>
  <!-- <div class="card">
    <button type="button" @click="count++">count is {{ count }}</button>
    <p>
      Edit
      <code>components/HelloWorld.vue</code> to test HMR
    </p>
  </div>

  <p>
    Check out
    <a href="https://vuejs.org/guide/quick-start.html#local" target="_blank"
      >create-vue</a
    >, the official Vue + Vite starter
  </p>
  <p>
    Install
    <a href="https://github.com/johnsoncodehk/volar" target="_blank">Volar</a>
    in your IDE for a better DX
  </p>
  <p class="read-the-docs">Click on the Vite and Vue logos to learn more</p> -->
</template>

<style scoped>
.read-the-docs {
  color: #888;
}
#clock {
  font-family: 'Share Tech Mono', monospace;
  text-align: center;
  position: absolute;
  left: 50%;
  top: 50%;
  -webkit-transform: translate(-50%, -50%);
          transform: translate(-50%, -50%);
  color: #daf6ff;
  text-shadow: 0 0 10px #0aafe6, 0 0 10px rgba(10, 175, 230, 0);
}
#clock .time {
  letter-spacing: 0.05em;
  font-size: 20px;
  padding: 5px 0;
}
</style>
