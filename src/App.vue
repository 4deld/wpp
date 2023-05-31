<script setup lang="ts">
import * as Hangul from 'hangul-js';
import { ref, onMounted, watch } from 'vue'
const p1p2_names = ref([''])
const s = ref("")
const key = ref(-1)
const x = ref(0)
function movetonext() {
  if (x.value < 6) {
    document.getElementById(String(x.value))?.focus()
  }
}
function updateinput(e: Event) {
  if (e.data != undefined) {
    s.value += e.data
    console.log(s.value)
    if (key.value === -1) {
      if (Hangul.isConsonant(s.value.slice(-2, -1)) && Hangul.isVowel(s.value.slice(-1))) {
        console.log(-1, s.value.slice(-2))
        let t = Hangul.assemble(s.value.slice(-2))
        s.value = s.value.slice(0, s.value.length - 2)
        s.value += t
        //p1_name.value[x.value] = t
      }
    }

    if (key.value === 0) {
      console.log(0, s.value)
      if (s.value.slice(-1) != s.value.slice(-2, -1)) {
        key.value = 1
      }
      if (x.value === 5) {
        p1p2_names.value[x.value] = Hangul.assemble(s.value.slice(-2))
        console.log(p1p2_names.value)
      }

    }
    else if (key.value === 1) {
      console.log(1, s.value)
      key.value = -1
      if (x.value === 0) {
        let arr = Hangul.disassemble(s.value.slice(-1))
        if (arr.length > 3) {
          p1p2_names.value[x.value] = Hangul.assemble(arr.slice(0,3))
          s.value = s.value.slice(0, s.value.length - 1)
          s.value += arr[3]
        }
        else {
          p1p2_names.value[x.value] = s.value.slice(-1)
        }

      }
      else if (Hangul.isConsonant(s.value.slice(-1))) {
        let t = Hangul.assemble(s.value.slice(-3, -1))
        if (t.length != 1) {
          t = s.value.slice(-2, -1)
        }

        p1p2_names.value[x.value] = t
      }
      else {
        p1p2_names.value[x.value + 1] = s.value.slice(-1)
      }
      x.value += 1
      movetonext()

    }
    if (Hangul.disassemble(s.value.slice(-1)).length == 2) {
      console.log(Hangul.disassemble(s.value.slice(-1)))
      let arr = Hangul.disassemble(s.value.slice(-1))
      if (Hangul.isConsonant(arr[1])) {
        s.value = s.value.slice(0, s.value.length - 1)
        s.value += arr[1]
      }
      else {
        p1p2_names.value[x.value] = s.value.slice(-1)
        key.value = 0
      }
    }
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
// watch(s,(newv,oldv)=>{
//   console.log(newv,oldv)
//   console.log(Hangul.disassemble(newv.slice(-1)))
//   if(Hangul.disassemble(newv.slice(-1)).length==2){
//   }
// })
</script>

<template>
  <div id="layout">
    <div class="title">이름 궁합 점수</div>
    <div class="inputs">
      <input id="0" type="text" :value="p1p2_names[0]" v-on:input="updateinput">
      <input id="3" type="text" :value="p1p2_names[3]" v-on:input="updateinput">
      <input id="1" type="text" :value="p1p2_names[1]" v-on:input="updateinput">
      <input id="4" type="text" :value="p1p2_names[4]" v-on:input="updateinput">
      <input id="2" type="text" :value="p1p2_names[2]" v-on:input="updateinput">
      <input id="5" type="text" :value="p1p2_names[5]" v-on:input="updateinput">
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
