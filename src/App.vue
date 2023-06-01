<script setup lang="ts">
import * as Hangul from 'hangul-js';
import { ref, onMounted, watch } from 'vue'
const p1p2_names = ref([''])
const namestonumber = ref([[]])
const s = ref("")
const key = ref(-1)
const x = ref(0)
onMounted(() => {
  document.getElementById('0')?.focus()
})
const Consonant = {
  'ㄱ': 2,
  "ㄴ": 2,
  "ㄷ": 3,
  "ㄹ": 1,
  "ㅁ": 3,
  "ㅂ": 2,
  "ㅅ": 4,
  "ㅇ": 4,
  "ㅈ": 4,
  "ㅊ": 4,
  "ㅋ": 2,
  "ㅌ": 4,
  "ㅍ": 4,
  "ㅎ": 4
};
const Vowel = {
  "ㅏ": 3,
  "ㅑ": 3,
  "ㅓ": 2,
  "ㅕ": 2,
  "ㅗ": 3,
  "ㅛ": 2,
  "ㅜ": 2,
  "ㅠ": 2,
  "ㅡ": 1,
  "ㅣ": 1,
  "ㅐ": 4,
  "ㅔ": 2,
  "ㅖ": 4,
  "ㅒ": 4,
}
function transform(s: string) {
  let arr = Hangul.disassemble(s)
  let rst = 0
  for (var i = 0; i < arr.length; i++) {
    if (Hangul.isVowel(arr[i])) { rst += Vowel[arr[i]] }
    else { rst += Consonant[arr[i]] }
  }
  return rst
}
function movetonext() {
  if (x.value < 6) {
    document.getElementById(String(x.value))?.focus()
  }
}

function xtov(g: number) {
  if (g === 1 || g === 2) { return g * 2 }
  if (g === 0 || g === 5) { return g }
  if (g === 3) { return 1 }
  return 3
}
function updateinput(e: Event) { //실시간으로 입력하는 한글을 event를 통해 볼 수 있음
  if (e.data != undefined) { //undefined가 아닌 경우
    s.value += e.data //문자열에 더해서 저장
    console.log(s.value)
    if (key.value === -1) { //자음 + 모음으로 이루어진 경우
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
      //한글의 특성상 length=2에서 최소 2개를 더 받아야 무슨 문자인지 판별할 수 있기때문에 단계를나눔
      //마지막글자는 최소2개를더받는게불가능해서 일단 받는대로 다 합침
      if (x.value === 5 || Hangul.isVowel(s.value.slice(-1))) {
        let t = Hangul.assemble(s.value.slice(-2)) //고 ㅘ
        if (t.length > 1) {
          let tt = Hangul.disassemble(s.value.slice(-2))
          tt[2] = tt[3]
          tt = tt.slice(0,tt.length-1)
          t = Hangul.assemble(tt)
        }
        p1p2_names.value[xtov(x.value)] = t
        s.value = s.value.slice(0, s.value.length - 1)
        s.value += p1p2_names.value[xtov(x.value)]
        console.log(p1p2_names.value)
      }
      else if (s.value.slice(-1) != s.value.slice(-2, -1)) {
        key.value = 1
      }
    }
    else if (key.value === 1) {
      console.log(1, s.value)
      key.value = -1
      if (x.value === 5) { //마지막글자는 최소2개를더받는게불가능해서 일단 받는대로 다 합침
        p1p2_names.value[xtov(x.value)] = Hangul.assemble(s.value.slice(-3))
        console.log(p1p2_names.value)
      }
      else if (x.value === 0) { //첫번째글자
        let arr = Hangul.disassemble(s.value.slice(-1))
        if (arr.length > 3) {
          p1p2_names.value[xtov(x.value)] = Hangul.assemble(arr.slice(0, 3))
          s.value = s.value.slice(0, s.value.length - 1)
          s.value += arr[3]
        }
        else {
          p1p2_names.value[xtov(x.value)] = s.value.slice(-1)
        }

      }
      //마지막이 자음 -> 종성이 존재하는 경우
      else if (Hangul.isConsonant(s.value.slice(-1))) {
        console.log("asdfasdf")
        let t = Hangul.assemble(s.value.slice(-3, -1))
        if (t.length != 1) {
          t = s.value.slice(-2, -1)
        }
        if (Hangul.isConsonant(t)) {
          t = Hangul.assemble(s.value.slice(-4, -1))
        }
        p1p2_names.value[xtov(x.value)] = t
      }
      else {
        p1p2_names.value[xtov(x.value + 1)] = s.value.slice(-1)
      }
      if (x.value < 5) {
        x.value += 1
      }
      movetonext()

    }
    //자음+모음 혹은 자음+자음 혹은 모음+모음으로 이루어진 경우
    if (Hangul.disassemble(s.value.slice(-1)).length == 2) {
      console.log(Hangul.disassemble(s.value.slice(-1)))
      let arr = Hangul.disassemble(s.value.slice(-1))
      if (Hangul.isConsonant(arr[1])) { //겹받침일 때 제거
        s.value = s.value.slice(0, s.value.length - 1)
        s.value += arr[1]
      }
      else {
        p1p2_names.value[xtov(x.value)] = s.value.slice(-1)
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

watch(x, () => {
  setTimeout(() => {
    document.getElementById(x.value - 1).disabled = 'true'
    namestonumber.value[0][xtov(x.value - 1)] = transform(p1p2_names.value[xtov(x.value - 1)])
  }, 100)
  if (x.value === 5) {
    setTimeout(() => {
      document.getElementById(5).disabled = 'true'
      namestonumber.value[0][5] = transform(p1p2_names.value[5])
    }, 1000)
  }
})

</script>

<template>
  <div id="layout">
    <div class="title">이름 궁합 점수</div>
    <div class="inputs">
      <input id="0" type="text" :value="p1p2_names[0]" v-on:input="updateinput">
      <input id="3" type="text" :value="p1p2_names[1]" v-on:input="updateinput">
      <input id="1" type="text" :value="p1p2_names[2]" v-on:input="updateinput">
      <input id="4" type="text" :value="p1p2_names[3]" v-on:input="updateinput">
      <input id="2" type="text" :value="p1p2_names[4]" v-on:input="updateinput">
      <input id="5" type="text" :value="p1p2_names[5]" v-on:input="updateinput">
    </div>
    <div>{{ p1p2_names }}</div>
    <div>{{ namestonumber }}</div>
    <div>
      <div></div>
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
