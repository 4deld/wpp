<script setup lang="ts">
import * as Hangul from 'hangul-js';
import { ref, onMounted, watch } from 'vue'
const p1_name = ref([])
const p2_name = ref([])
const nxtInput = ref(('p1_2nd'))
function movetonext(){
  document.getElementById(nxtInput.value)?.focus()
  if (nxtInput.value == 'p1_2nd') {
    nxtInput.value = 'p1_3rd'
  }
  else if (nxtInput.value == 'p1_3rd') {
    nxtInput.value = 'p2_1st'
  }
  else if (nxtInput.value == 'p2_1st') {
    nxtInput.value = 'p2_2nd'
  }
  else {
    nxtInput.value = 'p2_3rd'
  }
}
function updateinput(e: Event) {
  console.log(e.data)
  if (e.data != undefined) {
    if(Hangul.endsWithConsonant(e.data)){ //ㄱ or 각 => true

    }
    else{

    }
      p1_assemble(0)
  }
}
// watch(p1_name.value, (newv, oldv) => {
//   for(var i=0; i<p1_name.value.length; i++){
//     if(p1_name.value[i].length>1){
//       p1_name.value[i+1]=p1_name.value[i].slice(-1);
//       p1_name.value[i]=p1_name.value[i].slice(0,1);
//       movetonext()
//     }
//   }
// })
function p1_assemble(n: number) {
  console.log("p1a",n)
  if (p1_name.value[n] === undefined) return
  if (Hangul.assemble(p1_name.value[n]) != p1_name.value[n]) {
    p1_name.value[n] = Hangul.assemble(p1_name.value[n])
    return
  }
  if(!Hangul.isComplete(p1_name.value[n])) return
  movetonext()
}
</script>

<template>
  <div id="layout">
    <div class="title">이름 궁합 점수</div>
    <div class="inputs">
      <input id="p1_1st" type="text" v-model="p1_name[0]" v-on:input="updateinput">
      <input id="p2_1st" type="text" v-model="p2_name[0]">
      <input id="p1_2nd" type="text" v-model="p1_name[1]" v-on:input="updateinput">
      <input id="p2_2nd" type="text" v-model="p2_name[1]">
      <input id="p1_3rd" type="text" v-model="p1_name[2]" v-on:input="updateinput">
      <input id="p2_3rd" type="text" v-model="p2_name[2]">
    </div>
  </div>
</template>

<style scoped>
.title {
  font-size: 3em;
}

#layout {
  display: flex;
  flex-direction: column;
  align-items: center;
}

.inputs {
  display: flex;
  width: 50vw;
  height: 10vh;
}

input {
  text-align: center;
  font-size: 2em;
  margin: auto;
  width: 4vw;
  height: 7vh;
}
</style>
