# Notes
## Section 2, Program Structure
### nested block in if-else if-else block
```go
	if x, y := 100, 200; x > 1000 {
	} else if x := "hello"; y > 0 { //this x shadow the 'x' in if
		fmt.Println(x, y)
	}
```

### scope shaw issue
```go
	var cwd string
     func init() {
         cwd, err := os.Getwd() //compile error: the 'cwd' declared but not used
         if err != nil {
             log.Fatalf("os.Getwd failed: %v", err)
         }
	}
```

'cwd', 'err' are not declared in their block, so the compiler will declare them and will shadow the global 'cwd' variable.

## Section 3, Basic Data Type

### Go types
* basic types: numbers, strings, booleans
* aggregate types: arrays and structs
* reference types: slices, maps, functions, channels. (Each includes pointers that point to internal data)
* interface types 

### Types synonym
* int/uint types' size are platform and compiler dependent. ***Never make assumption for the size!***
* 'rune' is synonym to 'int32', while 'byte' to 'uint8'
* 'uintptr' is for the low level programming, like Go with C

### Operators
* the '%' for negative:
```go
    fmt.Println(-5 % -2) //-1
    fmt.Println(5 % -2) //1
    fmt.Println(-5 % 2) //-1
```
* the result is ***the same type as the operators***, so overflow may happen
```go
	var u uint8 = 255
    fmt.Println(u, u+1, u*u) // "255 0 1"
    var i int8 = 127
    fmt.Println(i, i+1, i*i) // "127 -128 1"
```
* 'unary operators': '+', '-', '^'
	** For integers, '+x' is short for '0 + x' while '-x' is short for '0 - x'
	** For floating and complex numbers, '+x' is just for 'x' while '-x' is the negative of 'x'
	** '^x' return the value with each bit inverted

* 'NaN' is from like '0/0' or 'sqrt(-1)'. Any comparation of 'NaN' always yield false
```go
    nan := math.NaN()
    fmt.Println(nan == nan, nan < nan, nan > nan) // "false false false"
```

* ***Row string literal***: no escape happens and can cross multiple lines

### Conversion and format

#### Printf
```go
	o := 0666
	fmt.Printf("%d %[1]o %#[1]o\n", o) // "438 666 0666"
	x := int64(0xdeadbeef)
	fmt.Printf("%d %[1]x %#[1]x %#[1]X\n", x)
	// Output:
	// 3735928559 deadbeef 0xdeadbeef 0XDEADBEEF
```
* The '[1]' means use the first argument, so no need to provide the same argument again and again
* The '#' is used to add the '0', '0x', '0X'
* If space is after the '%', like '% x', then it will insert the space for each hex digits, like 'e3 83 97 e3 83 ad e3 82 b0 e3 83 a9 e3 83 a0'
* The 'strconv' package includes many format functions

#### Unicode
* Unicode version 8 use 4 bytes for each charactor, also knows as UTF-32/UCS-4. In Go, the 'rune' is used for this.
* Unicode wasts lots of space, so the 'UTF-8' is invented.
```
00xxxxxx runes0−127 (ASCII)
111xxxxx 10xxxxxx 128−2047 (values <128 unused)
1110xxxx 10xxxxxx 10xxxxxx 2048−65535 (values <2048 unused)
11110xxx 10xxxxxx 10xxxxxx 10xxxxxx 65536−0x10ffff (other values unused)
```
* Code point
Same values:
```go
    "世界"
    "\xe4\xb8\x96\xe7\x95\x8c" //the binary using the UTF-8 coding
    "\u4e16\u754c" //the binary using the Unicode, will be encoded as UTF-8 by the compiler
    "\U00004e16\U0000754c" //the binary using the Unicode, will be encoded as UTF-8 by the compiler
```
They are all valid UTF-8 encoding of code point, but in Go, when for 'rune', value below 256 can used as '\x', while above 256, must use '\u' or '\U'.

### enumeratio
```go
const (
    _ = 1 << (10 * iota)
    KiB // 1024
    MiB // 1048576
    GiB // 1073741824
    TiB // 1099511627776 (exceeds 1 << 32)
    PiB // 1125899906842624
    EiB // 1152921504606846976
    ZiB // 1180591620717411303424
    YiB // 1208925819614629174706176
)
```
The 'iota' is 'int' type, so it will overflow.
