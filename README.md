# web-project

핵심 코드

알파벳을 입력하면 한 알파벳마다 바로 확인이 가능한 영어와 달리 한글의 경우 초성, 중성, 종성으로 나누어져 있어서 따로 function을 만들어서 event로 자음 혹은 모음 입력이 들어왔을 때 확인할 수 있다.

```javascript
function updateinput(e: Event) { //실시간으로 입력하는 한글을 event를 통해 볼 수 있음
  if (e.data != undefined) { //undefined가 아닌 경우
    s.value += e.data //문자열에 더해서 저장
    if (key.value === -1) { //자음 + 모음으로 이루어진 경우
      if (Hangul.isConsonant(s.value.slice(-2, -1)) && Hangul.isVowel(s.value.slice(-1))) {
        let t = Hangul.assemble(s.value.slice(-2))
        s.value = s.value.slice(0, s.value.length - 2)
        s.value += t
        //p1_name.value[x.value] = t
      }
    }

    if (key.value === 0) {
      //한글의 특성상 length=2에서 최소 2개를 더 받아야 무슨 문자인지 판별할 수 있기때문에 단계를나눔
      //마지막글자는 최소2개를더받는게불가능해서 일단 받는대로 다 합침
      if (x.value === 5 || Hangul.isVowel(s.value.slice(-1))) {
        let t = Hangul.assemble(s.value.slice(-2)) //고 ㅘ
        if (t.length > 1) {
          let tt = Hangul.disassemble(s.value.slice(-2))
          tt[2] = tt[3]
          tt = tt.slice(0, tt.length - 1)
          t = Hangul.assemble(tt)
        }
        p1p2_names.value[xtov(x.value)] = t
        s.value = s.value.slice(0, s.value.length - 1)
        s.value += p1p2_names.value[xtov(x.value)]
      }
      else if (s.value.slice(-1) != s.value.slice(-2, -1)) {
        key.value = 1
      }

      if (x.value === 0) {
        let t = Hangul.disassemble(s.value.slice(-1))
        if (Hangul.isVowel(t[2]) && t.length === 3) {
          key.value = 0
        }
      }
    }
    else if (key.value === 1) {
      key.value = -1
      if (x.value === 5) { //마지막글자는 최소2개를더받는게불가능해서 일단 받는대로 다 합침
        p1p2_names.value[xtov(x.value)] = Hangul.assemble(s.value.slice(-3))
      }
      else if (x.value === 0) { //첫번째글자
        let arr = Hangul.disassemble(s.value.slice(-1))
        if (arr.length > 3 && Hangul.isConsonant(arr[2])) { //겹받침인 경우
          p1p2_names.value[0] = Hangul.assemble(arr.slice(0, 3))
          s.value = s.value.slice(0, s.value.length - 1)
          s.value += arr[3]
        }
        else {
          p1p2_names.value[0] = s.value.slice(-1)
        }

      }
      //마지막이 자음 -> 종성이 존재하는 경우
      else if (Hangul.isConsonant(s.value.slice(-1))) {
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
```




## Recommended IDE Setup

[VSCode](https://code.visualstudio.com/) + [Volar](https://marketplace.visualstudio.com/items?itemName=Vue.volar) (and disable Vetur) + [TypeScript Vue Plugin (Volar)](https://marketplace.visualstudio.com/items?itemName=Vue.vscode-typescript-vue-plugin).

## Type Support for `.vue` Imports in TS

TypeScript cannot handle type information for `.vue` imports by default, so we replace the `tsc` CLI with `vue-tsc` for type checking. In editors, we need [TypeScript Vue Plugin (Volar)](https://marketplace.visualstudio.com/items?itemName=Vue.vscode-typescript-vue-plugin) to make the TypeScript language service aware of `.vue` types.

If the standalone TypeScript plugin doesn't feel fast enough to you, Volar has also implemented a [Take Over Mode](https://github.com/johnsoncodehk/volar/discussions/471#discussioncomment-1361669) that is more performant. You can enable it by the following steps:

1. Disable the built-in TypeScript Extension
    1) Run `Extensions: Show Built-in Extensions` from VSCode's command palette
    2) Find `TypeScript and JavaScript Language Features`, right click and select `Disable (Workspace)`
2. Reload the VSCode window by running `Developer: Reload Window` from the command palette.

## Customize configuration

See [Vite Configuration Reference](https://vitejs.dev/config/).

## Project Setup

```sh
npm install
```

### Compile and Hot-Reload for Development

```sh
npm run dev
```

### Type-Check, Compile and Minify for Production

```sh
npm run build
```

### Lint with [ESLint](https://eslint.org/)

```sh
npm run lint
```
