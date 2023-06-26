# In the middle of Rust

Tags: rev

we are given a [challenge.mir](./challenge.mir). Apparently `mir` (_mid-level_ intermediate reprsentation) is a transient file that the rust compiler uses. It consists of many, many functions (`func001`, `func002`, `func003`, etc...), and a main function.

We decided to do the stupid thing and manually reverse it by hand, painstakingly.

Let's dig into the main function first:
It first defines like 120 variables, but we can worry about that later. the nested `scope{ }` are also all useless.

We see a lot of `bb0`, `bb1`, etc, those are *basic blocks* inside the `.mir` file. They represent a sequence of instructions or statements, and determine how the program flows. Let's start with `bb0` in `main`:

```rust
bb0: {
    _1 = func001(const 126_u8) -> bb1;                                   
}
```
where `func001` is:
```rust
fn func001(_1: u8) -> u8 {
    debug ch => _1;                      
    let mut _0: u8;                      
    let mut _2: u8;                      
    let mut _3: (u8, bool);              
    let mut _4: u8;                      
    let mut _5: u8;                      
    let mut _6: (u8, bool);              
    let mut _7: (u8, bool);              
    scope 1 {
        debug retn => _0;                
    }

    bb0: {
        _0 = _1;                         
        _2 = _0;                         
        _3 = CheckedShr(_2, const 3_i32); // rshift _3 by 3
        assert(!move (_3.1: bool), "attempt to shift right by `{}`, which would overflow", const 3_i32) -> bb1; 
    }

    bb1: {
        _0 = move (_3.0: u8);
        _5 = _0;
        _6 = CheckedMul(_5, const 4_u8); // multiply _5 by 4
        assert(!move (_6.1: bool), "attempt to compute `{} * {}`, which would overflow", move _5, const 4_u8) -> bb2; 
    }

    bb2: {
        _4 = move (_6.0: u8);            
        _7 = CheckedAdd(_4, const 7_u8); // add 7 to _4
        assert(!move (_7.1: bool), "attempt to compute `{} + {}`, which would overflow", move _4, const 7_u8) -> bb3; 
    }

    bb3: {
        _0 = move (_7.0: u8);            
        return;                          
    }
}
```

Which is basically:
```rust
bb0: {
    _1 = (126 >> 3) * 4 + 7 -> bb1; // 67
}
```

Now let's see `bb1`:
```rust
bb1: {
    _3 = _1;                         
    _2 = move _3 as char (IntToInt); 
	_4 = func002(const 51_u8) -> bb2; 
}
```
we see it makes a a char labelled `_2`, which I assume is the characters of the flag. It also tries to feed `bb2`, the next block, with `func002(51)`, which will later become another character in the flag. From now on we just focused on evaluating these `funcxxx()` and its output.

The next several functions are all elementary ones, comprising of addition, subtraction, multiplication, division, and bitwise operators.

The next different one is `func010`:

```rust
fn func010(_1: u8) -> u8 {
    debug ch => _1;
    let mut _0: u8;
    let mut _2: std::ops::Range<i32>;
    let mut _3: std::ops::Range<i32>;
    let mut _5: std::option::Option<i32>;
    let mut _6: &mut std::ops::Range<i32>;
    let mut _7: isize;
    let mut _8: (u8, bool);
    scope 1 {
        debug retn => _0;
        let mut _4: std::ops::Range<i32>;
        scope 2 {
            debug iter => _4;
        }
    }

    bb0: {
        _0 = _1;                         
        _3 = std::ops::Range::<i32> { start: const 0_i32, end: const 10_i32 };  // loop of count 10
        _2 = <std::ops::Range<i32> as IntoIterator>::into_iter(move _3) -> bb1; 
    }

    bb1: {
        _4 = move _2;
        goto -> bb2;
    }

    bb2: {
        _6 = &mut _4;
        _5 = <std::ops::Range<i32> as Iterator>::next(_6) -> bb3;
    }

    bb3: {
        _7 = discriminant(_5);
        switchInt(move _7) -> [0: bb6, 1: bb4, otherwise: bb5];
    }

    bb4: {
        _8 = CheckedAdd(_0, const 1_u8); // add one
        assert(!move (_8.1: bool), "attempt to compute `{} + {}`, which would overflow", _0, const 1_u8) -> bb7;
    }

    bb5: {
        unreachable;
    }

    bb6: {
        return;                     
    }

    bb7: {
        _0 = move (_8.0: u8);
        goto -> bb2;
    }
}
```

It is essentially a for loop of 10, and each time it loops `_0` gets incremented by 1 (via `_8`)
Therefore `func010(109) = 119` which corresponds to ``'w'``

The next several functions are also elementary ones, comprising of addition, subtraction, multiplication, division, and bitwise operators, and also exponentiation.

The next different one is `func018`:

```rust
fn func018(_1: u8) -> u8 {
    debug ch => _1;
    let mut _0: u8;
    let mut _3: u8;
    let mut _4: usize;
    let mut _5: u8;
    let mut _6: (u8, bool);
    scope 1 {
        debug retn => _0;
        let _2: &str;
        scope 2 {
            debug s => _2;
        }
    }

    bb0: {
        _0 = _1;
        _2 = const "fightingkeepgoing";
        _4 = core::str::<impl str>::len(_2) -> bb1; // gets string length (17)
    }

    bb1: {
        _3 = move _4 as u8 (IntToInt);
        _5 = _0;
        _6 = CheckedAdd(_3, _5); // input + 17
        assert(!move (_6.1: bool), "attempt to compute `{} + {}`, which would overflow", move _3, move _5) -> bb2;
    }

    bb2: {
        _0 = move (_6.0: u8);
        return;
    }
}
```

It creates a string `"fightingkeepgoing"`, gets the length of the string (17) and adds it to the input.
Hence `func018(50) = 67` which as a char is `'C'`.

`func020` is almost the same as `func010` except it increments by 3 each time instead of 1, therefore `func020(54) = 84` which is `'T'`.

The next different function is `func030`:

```rust
fn func030(_1: u8) -> u8 {
    debug ch => _1;
    let mut _0: u8;
    let mut _3: (i32, bool);
    let mut _4: (u8, bool);
    let mut _5: i32;
    scope 1 {
        debug retn => _0;
        let mut _2: i32;
        scope 2 {
            debug chk => _2;
        }
    }

    bb0: {
        _0 = _1;
        _2 = const 0_i32;
        goto -> bb1;
    }

    bb1: {
        _3 = CheckedAdd(_2, const 1_i32);
        assert(!move (_3.1: bool), "attempt to compute `{} + {}`, which would overflow", _2, const 1_i32) -> bb2;
    }

    bb2: {
        _2 = move (_3.0: i32);
        _4 = CheckedAdd(_0, const 1_u8);
        assert(!move (_4.1: bool), "attempt to compute `{} + {}`, which would overflow", _0, const 1_u8) -> bb3;
    }

    bb3: {
        _0 = move (_4.0: u8);
        _5 = _2;
        switchInt(move _5) -> [8: bb4, otherwise: bb1];
    }

    bb4: {
        return;
    }
}
```

`_2` is initialized at 0.
`bb1` and `bb2` increments `_2` and `_0` by 2 each time, and `bb3` checks if `_2` is 8. So the end result is the input incremented by 8. Therefore `func030(100) = 108` which is `'1'`

The last weird function is `func033`;

```rust
fn func033(_1: u8) -> u8 {
    debug ch => _1;                      
    let mut _0: u8;                      
    let mut _2: std::ops::Range<i32>;    
    let mut _3: std::ops::Range<i32>;    
    let mut _5: std::option::Option<i32>; 
    let mut _6: &mut std::ops::Range<i32>; 
    let mut _7: isize;                   
    let mut _9: i32;                     
    let mut _10: (u8, bool);             
    scope 1 {
        debug retn => _0;                
        let mut _4: std::ops::Range<i32>; 
        scope 2 {
            debug iter => _4;            
            let _8: i32;                 
            scope 3 {
                debug i => _8;           
            }
        }
    }

    bb0: {
        _0 = _1;  // 106                       
        _3 = std::ops::Range::<i32> { start: const 0_i32, end: const 10_i32 }; 
        _2 = <std::ops::Range<i32> as IntoIterator>::into_iter(move _3) -> bb1; 
                                         
    }

    bb1: {
        _4 = move _2;                    
        goto -> bb2;                     
    }

    bb2: {
        _6 = &mut _4;                    
        _5 = <std::ops::Range<i32> as Iterator>::next(_6) -> bb3; 
    }

    bb3: {
        _7 = discriminant(_5);           
        switchInt(move _7) -> [0: bb6, 1: bb4, otherwise: bb5]; 
    }

    bb4: {
        _8 = ((_5 as Some).0: i32);      
        _9 = Rem(_8, const 2_i32);       
        switchInt(move _9) -> [0: bb7, otherwise: bb2]; 
    }

    bb5: {
        unreachable;                     
    }

    bb6: {
        return;                          
    }

    bb7: {
        _10 = CheckedAdd(_0, const 1_u8); 
        assert(!move (_10.1: bool), "attempt to compute `{} + {}`, which would overflow", _0, const 1_u8) -> bb8; 
    }

    bb8: {
        _0 = move (_10.0: u8);           
        goto -> bb2;                     
    }
}
```

This can be re-written as:

```js
func033(input){
    for (let i = 0;i < 10;i++){
        if(i%2 == 0){
            input += 1
        }
    }
}
```

Therefore `func033(106) = 111` which is `'o'`

Final flag: `CDDC2023{w0w_YOU_CuT_cR4b_Be1ly_oP3N}`
