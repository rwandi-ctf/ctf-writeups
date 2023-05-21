# Web-Assembly
We are given the following wasm code:
```js
const wasmBinBuf = new Uint8Array([0,97,115,109,1,0,0,0,1,5,1,96,0,1,127,2,11,1,2,106,115,3,109,101,109,2,0,1,3,2,1,0,7,9,1,5,99,104,101,99,107,0,0,10,122,1,120,1,3,127,65,0,33,0,65,1,33,2,3,64,2,64,2,64,2,64,2,64,2,64,32,0,65,4,112,14,3,3,2,1,0,11,65,137,2,33,1,12,3,11,65,59,33,1,12,2,11,65,41,33,1,12,1,11,65,31,33,1,12,0,11,32,1,65,255,1,32,0,40,2,0,113,108,65,255,1,113,32,0,65,192,0,106,40,2,0,65,255,1,113,115,65,0,70,32,2,108,33,2,32,0,65,1,106,33,0,32,0,65,46,72,13,0,11,32,2,11]);
```
I wrote it to a file called `wasm.wasm` using:
```js
const fs = require('fs')
fs.writeFileSync('wasm.wasm', wasmBinBuf)
```
I then uploaded it to https://webassembly.github.io/wabt/demo/wasm2wat/ and got this abomination:
```
(module
  (type $t0 (func (result i32)))
  (import "js" "mem" (memory $js.mem 1))
  (func $check (export "check") (type $t0) (result i32)
    (local $l0 i32) (local $l1 i32) (local $l2 i32)
    (local.set $l0
      (i32.const 0))
    (local.set $l2
      (i32.const 1))
    (loop $L0
      (block $B1
        (block $B2
          (block $B3
            (block $B4
              (block $B5
                (br_table $B2 $B3 $B4 $B5
                  (i32.rem_u
                    (local.get $l0)
                    (i32.const 4))))
              (local.set $l1
                (i32.const 265))
              (br $B1))
            (local.set $l1
              (i32.const 59))
            (br $B1))
          (local.set $l1
            (i32.const 41))
          (br $B1))
        (local.set $l1
          (i32.const 31))
        (br $B1))
      (local.set $l2
        (i32.mul
          (i32.eq
            (i32.xor
              (i32.and
                (i32.mul
                  (local.get $l1)
                  (i32.and
                    (i32.const 255)
                    (i32.load
                      (local.get $l0))))
                (i32.const 255))
              (i32.and
                (i32.load
                  (i32.add
                    (local.get $l0)
                    (i32.const 64)))
                (i32.const 255)))
            (i32.const 0))
          (local.get $l2)))
      (local.set $l0
        (i32.add
          (local.get $l0)
          (i32.const 1)))
      (br_if $L0
        (i32.lt_s
          (local.get $l0)
          (i32.const 46))))
    (local.get $l2)))

```
It was a bother to read ~~so I asked ChatGPT+ to explain the code~~
It was basically a `check` fn that reads the provided `mem`, of which the first 64 bits was the expected str input and the rest was the `data`
```js
function check(memory) {
  const arr = [31, 41, 59, 265]
  for (let l0 = 0; l0 < 46; l0++) {
    l1 = arr[l0 % 4]
    l2 *= ((l1 * memory[l0]) & 255) ^ (memory[l0 + 64] & 255)) === 0 ? 1 : 0;
  }
  return l2;
}
```
Then let's make a solver, using the fact that `x&255 = x%256` 
```js
let data = [121,66,71,65,229,176,150,150,43,107,209,212,12,217,16,222,129,189,55,185,82,127,229,47,45,178,252,11,107,43,31,114,20,97,229,185,237,55,252,87,12,168,75,222,121,5]

arr = [31, 41, 59, 265]
input = data.map((number, l0)=>{
    l1 = arr[l0%4]
    for(let n = 0;n<127;n++){
        if((number+256*n)%l1 == 0){
            return (number+256*n)/l1
        }
    }
})

str = ''
input.forEach(e=>str+=String.fromCharCode(e))
console.log(str) // grey{0bfusc4t10n_u51ng_w3b4s53mbly_1s_4_th1ng}
```
