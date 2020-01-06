# Lab 0: Go Intro

[source](https://tour.golang.org/list)

## Basics
### Packages, variables and functions
- packages
  - starts in `main`
- imports
  ```go
  import "fmt"
  import "math"
  ```
  - better to use:
  ```go
  import (
	  "fmt"
	  "math"
  )
  ```
- exported names
  - begins with a capital letter
  - `math.pi` is wrong. `math.Pi` is correct.
- functions
  - type comes after variable name
  ```go
  func add(x int, y int) int {
    return x + y
  }
  ```
  - if two or more variables share the same type
  ```go
  func add(x, y int) int {
    return x + y
  }
  ```
- multiple results
  - similar as python
  ```go
  func swap(x, y string) (string, string) {
    return y, x
  }
  ```
- named return values
  - only for short functions, return values may be named. "naked" return.
  ```go
  func split(sum int) (x, y int) {
    x = sum * 4 / 9
    y = sum - x
    return
  }
  func main() {
    var x, y int = split(17)
  }
  ```
- variables
  - declaration
  ```go
  var c, python, java bool
  ```
  - can be at package or function level
  - with initializers
    - can omit type. type follows the initializer
    ```go
    var c, python, java = true, false, "no!"
    ```
  - short variable declaration
    - **inside functions**, `var` declaration of implicit type
    ```go
    k := 3
    ```
- basic types
```
bool

string

int  int8  int16  int32  int64
uint uint8 uint16 uint32 uint64 uintptr

byte // alias for uint8

rune // alias for int32
     // represents a Unicode code point

float32 float64

complex64 complex128
```
- zero values
  - default
  - `0`, `false`, `""`
- type conversions
  - `T(v)` converts `v` to type `T`
- type inference
- constants
  - `const`
  - different types
  - cannot use `:=`

### Flow control statements
- for
  - init, condition, post
  ```go
  sum := 0
  for i := 0; i < 10; i++ {
    sum += i
  }
  ```
  - optional init and post
  ```go
  sum := 1
  for ; sum < 1000; {
    sum += sum
  }
  ```
  - `for` is go's `while`
  ```go
  sum := 1
  for sum < 1000 {
    sum += sum
  }
  ```
  - forever
  ```go
  for {
  }
  ```
- if
  - regular if
  ```go
  if x < 0 {
    return sqrt(-x) + "i"
  }
  ```
  - if with a short statement
  ```go
  if v := math.Pow(x, n); v < lim {
    return v
  }
  ```
    - scope of `v` only by the end of `if` statement
  - if and else
  ```go
  if v := math.Pow(x, n); v < lim {
    return v
  } else {
    fmt.Printf("%g >= %g\n", v, lim)
  }
  ```
- switch
  - in go, it runs the selected case
  - go's switch cases need not be constants, and values involved need not be integers
  ```go
  switch os := runtime.GOOS; os {
  case "darwin":
    fmt.Println("OS X.")
  case "linux":
    fmt.Println("Linux.")
  default:
    // freebsd, openbsd,
    // plan9, windows...
    fmt.Printf("%s.\n", os)
  }
  ```
  - evaluates from top to bottom, stopping when a case succeeds
  - switch with no condition
    - can be a clean way to write long if-else chains
    ```go
    switch {
    case t.Hour() < 12:
      fmt.Println("Good morning!")
    case t.Hour() < 17:
      fmt.Println("Good afternoon.")
    default:
      fmt.Println("Good evening.")
    }
    ```
- defer
  - A defer statement defers the execution of a function until the surrounding function returns.
  - The deferred call's arguments are evaluated immediately, but the function call is not executed until the surrounding function returns.
  - stacking defers
  ```go
  fmt.Println("counting")
  for i := 0; i < 10; i++ {
    defer fmt.Println(i)
  }
  fmt.Println("done")
  ```
    - prints:
    ```
    counting
    done
    9
    8
    7
    6
    5
    4
    3
    2
    1
    0
    ```
### Structs, slices, and maps
- pointers
  - type `*T` is a pointer to a `T` value. default: `nil`
  ```go
  var p *int
  ```
  - `&` operator generates a pointer to its operand
  ```go
  i := 42
  p = &i
  ```
  - `*` operator denotes the pointer's underlying value
    - *dereferencing* or *indirecting*
  ```go
  fmt.Println(*p) // read i through the pointer p
  *p = 21         // set i through the pointer p
  ```
- structs
  - a collection of fields
  ```go
  type Vertex struct {
    X int
    Y int
  }
  ```
  - **struct fields** are accessed using a dot
  ```go
  v := Vertex{1, 2}
  v.X = 4
  ```
  - struct literals
  ```go
  var (
    v1 = Vertex{1, 2}  // has type Vertex
    v2 = Vertex{X: 1}  // Y:0 is implicit
    v3 = Vertex{}      // X:0 and Y:0
    p  = &Vertex{1, 2} // has type *Vertex
  )
  ```
- arrays
  - type `[n]T` is an array of n values of type `T`
  ```go
  var a [10]int
  ```
  - cannot be resized
  ```go
  var a [2]string
  a[0] = "Hello"
  a[1] = "World"
  fmt.Println(a[0], a[1])
  fmt.Println(a)

  primes := [6]int{2, 3, 5, 7, 11, 13}
  ```
- slices
  - type `[]T` is a slice with elements of type `T`
  ```go
  primes := [6]int{2, 3, 5, 7, 11, 13}
  var s []int = primes[1:4]
  ```
  - slices are like references to arrays
    - do not store data
    - changing elements of a slice modifies its underlying array
    - other slices that share the same array will see these changes
  - slice literals
    - like array literals without a length
    - array literal: `[3]bool{true, true, false}`
    - slice literal: `[]bool{true, true, false}`
  - length and capacity
    - `len(s)` vs `cap(s)`
  - default: `nil`
  - creating a slice with `make`
  ```go
  a := make([]int, 5)  // len(a)=5
  b := make([]int, 0, 5) // len(b)=0, cap(b)=5
  b = b[:cap(b)] // len(b)=5, cap(b)=5
  b = b[1:]      // len(b)=4, cap(b)=4
  ```
  - appending to a slice
  ```go
  func append(s []T, vs ...T) []T
  ```
    - The returned slice will point to the newly allocated array.
  - range
  ```go
  var pow = []int{1, 2, 4, 8, 16, 32, 64, 128}
  for i, v := range pow {
    fmt.Printf("2**%d = %d\n", i, v)
  }
  ```
    - can skip index of value by assigning to `_`
    ```go
    for i, _ := range pow
    for _, value := range pow
    ```
    - can omit second variable if only want index
    ```go
    for i := range pow
    ```
- maps
  - `nil` map has no key nor keys can be added
  - `make` creates a map
  ```go
  type Vertex struct {
    Lat, Long float64
  }
  m = make(map[string]Vertex)
  m["Bell Labs"] = Vertex{
    40.68433, -74.39967,
  }
  fmt.Println(m["Bell Labs"])
  ```
  - mutating maps
    - insert or update
    ```go
    m[key] = elem
    ```
    - retrieve
    ```go
    elem = m[key]
    ```
    - delete an element
    ```go
    delete(m, key)
    ```
    - test a key is present
    ```go
    elem, ok = m[key]
    ```
- function values
- function closures

## Methods and Interfaces
- methods
  - no classes
  - a method is a function with a special receiver argument
    - `func` + **receiver argument** + *method name* + *return type*
  - compare function and methods
    - function
    ```go
    func add(x int, y int) int {
      return x + y
    }
    ```
    - method
    ```go
    func (v Vertex) Abs() float64 {
      return math.Sqrt(v.X*v.X + v.Y*v.Y)
    }
    ```
      - to call this function
      ```go
      add(42, 13)
      ```
      - to call this method
      ```go
      v := Vertex{3, 4}
      fmt.Println(v.Abs())
      ```
- pointer receivers
  - receiver is `*T` for some type `T`
  - Methods with pointer receivers can modify the value to which the receiver points.
- methods and pointer indirection
- interfaces
  - An interface type is defined as a set of method signatures.
  ```go
  type Abser interface {
    Abs() float64
  }
  ```
  - interfaces are implemented implicitly
  - interface values = tuple of a value and a concrete type
  - nil
  - empty interfaces
    - Empty interfaces are used by code that handles values of unknown type.
- type assertions
  - access an interface value's underlying type
  ```go
  t, ok := i.(T)
  ```
- type switches
```go
switch v := i.(type) {
case T:
    // here v has type T
case S:
    // here v has type S
default:
    // no match; here v has the same type as i
}
```
- errors
