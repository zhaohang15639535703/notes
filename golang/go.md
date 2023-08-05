# https://go.dev/tour/

## Basics

### 1.Package

- Every Go program is made up of packages
- program start with package **main**



By convention, the package name is the same as the last element of the import path.

 For instance, the "**math/rand**" package comprises files that begin with the statement **package rand.**



### 2.Imports

```go
import (
	"fmt"
	"math"
)
// also like:
import "fmt"
import "math"
```

### 3.Exported names

A name is exported if it begin with a capital letter.

```go
package main

import (
	"fmt"
	"math"
)

func main() {
    // error:undefined: math.pi
	fmt.Println(math.pi)
    // 3.141592653589793
    fmt.Println(math.Pi)
}
```

### 4.Functions

Notice that the type comes *after* the variable name.

```go
package main

import "fmt"

func add(x int, y int) int {
	return x + y
}

func main() {
	fmt.Println(add(42, 13))
}
```

**Syntactic Sugar**

When two or more consecutive named function parameters share a type, you can omit the type from all but the last.

```go
func add(x, y int) int {
	return x + y
}
```

**Multiple results**

A function can return any number of results

```go
package main

import "fmt"

func swap(x, y string) (string, string) {
	return y, x
}

func main() {
	a, b := swap("hello", "world")
	fmt.Println(a, b)
}
```

**Named return values**

Go's return values may be **named.**

If so, they are treated as **variables defined at the top of the function.**

A return statement without arguments returns the named return values. This is known as a "naked" return.

```go
package main

import "fmt"

func split(sum int) (x, y int) {
	x = sum * 4 / 9
	y = sum - x
	return
}

func main() {
	fmt.Println(split(17))
}
```

Suggestions**: Naked return statements** should be used only in short functions, as with the example shown here. They can harm readability in longer functions.

### 5.Variables

- The var statement declares a list of variables;
-  as in function argument lists, the type is last.

```go
package main

import "fmt"

//A var statement can be at package or function level.
var c, python, java bool

func main() {
	var i int
	fmt.Println(i, c, python, java)
}
```

**Initializer**

A var declaration can include initializers, one per variable. 

If an initializer is present, the type can be omitted; the variable will take the type of the initializer. 

```go
package main

import "fmt"

var i, j int = 1, 2
//var i, j  = 1, 2

func main() {
	var c, python, java = true, false, "no!"
	fmt.Println(i, j, c, python, java)
}
```



Variables declared without an explicit initial value are given their ***zero value\*****.**

The zero value is: 

- 0 for numeric types,
- false for the boolean type, and
- "" (the empty string) for strings.

```go
package main

import "fmt"

func main() {
	var i int
	var f float64
	var b bool
	var s string
    //0 0 false ""
	fmt.Printf("%v %v %v %q\n", i, f, b, s)
}
```





**Short variable declarations**

- **Inside** a function, the **" := "** short assignment statement can be used in place of a var declaration with implicit type.
- **Outside** a function, **every statement begins with a keyword** (var, func, and so on) and so the := construct is not available. 

```go
package main

import "fmt"

func main() {
	var i, j int = 1, 2
	k := 3
	c, python, java := true, false, "no!"

	fmt.Println(i, j, k, c, python, java)
}
```

### 6.Basic Types

```go
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

- The int, uint, and uintptr types are usually 32 bits wide on 32-bit systems and 64 bits wide on 64-bit systems. 
- When you need an integer value you should use int unless you have a specific reason to use a sized or unsigned integer type.

example

```go
package main

import (
	"fmt"
	"math/cmplx"
)

var (
	ToBe   bool       = false
	MaxInt uint64     = 1<<64 - 1
	z      complex128 = cmplx.Sqrt(-5 + 12i)
)

func main() {
	fmt.Printf("Type: %T Value: %v\n", ToBe, ToBe)
	fmt.Printf("Type: %T Value: %v\n", MaxInt, MaxInt)
	fmt.Printf("Type: %T Value: %v\n", z, z)
}
```

**Type conversions**

The expression T(v) converts the value v to the type T.

```go
package main

import (
	"fmt"
	"math"
)



func main() {
// Some numeric conversions:

// var i int = 42
// var f float64 = float64(i)
// var u uint = uint(f)

// Or, put more simply:

// i := 42
// f := float64(i)
// u := uint(f)
    
	var x, y int = 3, 4
	var f float64 = math.Sqrt(float64(x*x + y*y))
	var z uint = uint(f)
	fmt.Println(x, y, z)
}
```

Unlike in C, in Go assignment between items of different type **requires an explicit conversion**



**Type Inference**

```go
var i int
j := i // j is an int

// when the right hand side contains an untyped numeric constant, the new variable may 
// be an int, float64, or complex128 depending on the precision of the constant: 
i := 42           // int
f := 3.142        // float64
g := 0.867 + 0.5i // complex128
```



### 7.Constants

- Constants are declared like variables, but with the const keyword. 
- Constants can be character, string, boolean, or numeric values. 
- Constants cannot be declared using the := syntax. 



```go
package main

import "fmt"

const Pi = 3.14

func main() {
	const World = "世界"
	fmt.Println("Hello", World)
	fmt.Println("Happy", Pi, "Day")

	const Truth = true
	fmt.Println("Go rules?", Truth)
}
```



**Numeric Constants**

- Numeric constants are **high-precision** *values*.



An untyped constant takes the type needed by its context.

```go
import "fmt"

const (
	// Create a huge number by shifting a 1 bit left 100 places.
	// In other words, the binary number that is 1 followed by 100 zeroes.
	Big = 1 << 100
	// Shift it right again 99 places, so we end up with 1<<1, or 2.
	Small = Big >> 99
)

func needInt(x int) int { return x*10 + 1 }
func needFloat(x float64) float64 {
	return x * 0.1
}

func main() {
	fmt.Println(needInt(Small))
	fmt.Println(needFloat(Small))
	fmt.Println(needFloat(Big))
}
```



### 8.For 

Go has only one looping construct, the for loop.



The basic for loop has three components separated by semicolons: 

- the init statement: executed before the first iteration
- the condition expression: evaluated before every iteration
- the post statement: executed at the end of every iteration

```go
package main

import "fmt"

func main() {
	sum := 0
    //there are no parentheses surrounding the three components of the for 
    //statement and the braces { } are always required
	for i := 0; i < 10; i++ {
		sum += i
	}
	fmt.Println(sum)
}
```

but the init and post statements are optional.

```go
package main

import "fmt"

func main() {
	sum := 1
	for ; sum < 1000; {
		sum += sum
	}
	fmt.Println(sum)
}
```

**For is Go's "while"**

```go
package main

import "fmt"

func main() {
	sum := 1
	for sum < 1000 {
		sum += sum
	}
	fmt.Println(sum)
}
```

**Forever**

```go
package main

func main() {
	for {
	}
}
```

### 9.If

Go's if statements are like its for loops; the expression need not be surrounded by parentheses ( ) but the braces { } are required.

```go
package main

import (
	"fmt"
	"math"
)

func sqrt(x float64) string {
	if x < 0 {
		return sqrt(-x) + "i"
	}
	return fmt.Sprint(math.Sqrt(x))
}

func main() {
	fmt.Println(sqrt(2), sqrt(-4))
}
```

**If with a short statement**

- Like for, the if statement can start with a short statement to execute before the condition. 
- Variables declared by the statement are only in scope until the end of the if. 

```go
package main

import (
	"fmt"
	"math"
)

func pow(x, n, lim float64) float64 {
	if v := math.Pow(x, n); v < lim {
		return v
	}else {
		fmt.Printf("%g >= %g\n", v, lim)
	}
	return lim
}

func main() {
	fmt.Println(
		pow(3, 2, 10),
		pow(3, 3, 20),
	)
}
```

Variables declared inside an if short statement are also available inside any of the else blocks.

### 10.Switch

Go's switch is like the one in C, C++, Java, JavaScript, and PHP, except that **Go only runs the selected case, not all the cases that follow**.

```go
package main

import (
	"fmt"
	"runtime"
)

func main() {
	fmt.Print("Go runs on ")
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
}
```

Switch cases evaluate cases **from top to bottom**, stopping when a case succeeds.



- Switch without a condition is the same as switch true.

This construct can be **a clean way to write long if-then-else chains.**

```go
package main

import (
	"fmt"
	"time"
)

func main() {
	t := time.Now()
	switch {
	case t.Hour() < 12:
		fmt.Println("Good morning!")
	case t.Hour() < 17:
		fmt.Println("Good afternoon.")
	default:
		fmt.Println("Good evening.")
	}
}
```

### 11.Defer

- A defer statement **defers the execution of a function** until the surrounding function returns.
- The deferred call's arguments are evaluated immediately, but the function call is not executed until the surrounding function returns.

```go
package main

import "fmt"

func main() {
	defer fmt.Println("world")

	fmt.Println("hello")
}
//print:
//hello
// world
```

Deferred function calls are pushed onto a **stack**. When a function returns, its deferred calls are executed in **last-in-first-out** order.

```go
package main

import "fmt"

func main() {
	fmt.Println("counting")

	for i := 0; i < 10; i++ {
		defer fmt.Println(i)
	}

	fmt.Println("done")
}


// counting
// done
// 9
// 8
// 7
// 6
// 5
// 4
// 3
// 2
// 1
// 0
```



### 12.Pointers

 A pointer holds the memory address of a value

The type *T is a pointer to a T value. Its zero value is nil.

```go
var p *int
i := 42
p = &i
//this is called dereferencing or indirecting
fmt.Println(*p)
*p = 21
```

- Unlike C, **Go has no pinter arithmetic**



### 13.Struct

A struct is a collection of fields

```go
package main

import "fmt"

type Vertex struct {
	X int
	Y int
}

func main() {
	fmt.Println(Vertex{1, 2})
}
```

- the struct fields are accessed using a dot.
- the struct fields can be accessed by a pointer through a struct pointer.Like Rust,you can use p.x instead of (*p).x , without the explicit dereference



**struct literals**

a struct literal denotes a newly allocated struct value by listing the values of its fields.

```go
package main

import "fmt"

type Vertex struct{
    X,Y int
}

var (
    v1 = Vertex{1,2}
    v2 = Vertex{X:1}
    v3 = Vertex{}
    p = &Vertex{1,2}
)

func main(){
	fmt.Println(v1,p,v2,v3)
}
```

### 14.Array

The type **[n]T** is an array of n values of type T

```go
package main

import "fmt"

func main() {
	var a [2]string
	a[0] = "Hello"
	a[1] = "World"
	fmt.Println(a[0], a[1])
	fmt.Println(a)

	primes := [6]int{2, 3, 5, 7, 11, 13}
	fmt.Println(primes)
}
```

- An array's length is part of its type, so **arrays cannot be resized**.



### 15.Slices

like rust

A slice is formed by specifying two indices a low and high bound,separated by a colon,

```go
a[low:high]
```

- this selects a half-open range which includes the first element,but excludes the last one

```go
package main

import "fmt"

func main() {
	primes := [6]int{2, 3, 5, 7, 11, 13}

	var s []int = primes[1:4]
	fmt.Println(s)
}
```

- a slice does not store any data,it just describes a section of an underlying array
- Changing the elements of a slice modifies the corresponding elements of its underlying array.



**slice literals:**

A slice literal is like an array literal without the length

```go
package main

import "fmt"

func main() {
	q := []int{2, 3, 5, 7, 11, 13}
	fmt.Println(q)

	r := []bool{true, false, true, true, false, true}
	fmt.Println(r)

	s := []struct {
		i int
		b bool
	}{
		{2, true},
		{3, false},
		{5, true},
		{7, true},
		{11, false},
		{13, true},
	}
	fmt.Println(s)
}
```

like rust,the slice has defaults

these slice expressions are equivalent: 

```go
a[0:10]
a[:10]
a[0:]
a[:]
```



**length** and **capacity**

A slice has both a *length* and a *capacity*.

- The length of a slice is the number of elements it contains. 
- The capacity of a slice is the number of elements in the underlying array, counting from the first element in the slice. 
- The length and capacity of a slice s can be obtained using the expressions len(s) and cap(s).
- You can extend a slice's length by re-slicing it, provided it has sufficient capacity.

```go
package main

import "fmt"

func main() {
	s := []int{2, 3, 5, 7, 11, 13}
	printSlice(s)

	// Slice the slice to give it zero length.
	s = s[:0]
	printSlice(s)

	// Extend its length.
	s = s[:4]
	printSlice(s)

	// Drop its first two values.
	s = s[2:]
	printSlice(s)
}

func printSlice(s []int) {
	fmt.Printf("len=%d cap=%d %v\n", len(s), cap(s), s)
}
```



**Nil slices**

The zero value of a slice is nil

A nil slice has a length and capacity of 0 and has no underlying array.

```go
package main

import "fmt"

func main() {
	var s []int
	fmt.Println(s, len(s), cap(s))
	if s == nil {
		fmt.Println("nil!")
	}
}
```



**Creating a slice with make**

Slices can be created with the built-in make function; **this is how you create dynamically-sized arrays.**

- The make function allocates a zeroed array and returns a slice that refers to that array:

```go
a := make([]int, 5)  // len(a)=5
```

- To specify a capacity, pass a third argument to make:

```go
b := make([]int, 0, 5) // len(b)=0, cap(b)=5

b = b[:cap(b)] // len(b)=5, cap(b)=5
b = b[1:]      // len(b)=4, cap(b)=4
package main

import "fmt"

func main() {
	a := make([]int, 5)
	printSlice("a", a)

	b := make([]int, 0, 5)
	printSlice("b", b)

	c := b[:2]
	printSlice("c", c)

	d := c[2:5]
	printSlice("d", d)
}

func printSlice(s string, x []int) {
	fmt.Printf("%s len=%d cap=%d %v\n",
		s, len(x), cap(x), x)
}
```



**slices of slices**

slices can contain any type,including other slices

```go
package main

import (
	"fmt"
	"strings"
)

func main() {
	// Create a tic-tac-toe board.
	board := [][]string{
		[]string{"_", "_", "_"},
		[]string{"_", "_", "_"},
		[]string{"_", "_", "_"},
	}

	// The players take turns.
	board[0][0] = "X"
	board[2][2] = "O"
	board[1][2] = "X"
	board[1][0] = "O"
	board[0][2] = "X"

	for i := 0; i < len(board); i++ {
		fmt.Printf("%s\n", strings.Join(board[i], " "))
	}
}
```



**Appending to a slice**

```go
func append(s []T, vs ...T) []T
```

The first parameter s of append is a slice of type T, and the rest are T values to append to the slice.

```go
package main

import "fmt"

func main() {
	var s []int
	printSlice(s)

	// append works on nil slices.
	s = append(s, 0)
	printSlice(s)

	// The slice grows as needed.
	s = append(s, 1)
	printSlice(s)

	// We can add more than one element at a time.
	s = append(s, 2, 3, 4)
	printSlice(s)
}

func printSlice(s []int) {
	fmt.Printf("len=%d cap=%d %v\n", len(s), cap(s), s)
}
```

### 16.Range

the range form of the for loop iterates over a slice or map

like rust,When ranging over a slice, two values are returned for each iteration. The first is the index, and the second is a copy of the element at that index.

```go
package main

import "fmt"

var pow = []int{1, 2, 4, 8, 16, 32, 64, 128}

func main() {
	for i, v := range pow {
		fmt.Printf("2**%d = %d\n", i, v)
	}
}
```

You can skip the index or value by assigning to _.

```go
for i, _ := range pow for _, value := range pow
```



If you only want the index, you can omit the second variable. 

```go
for i := range pow
```

### 17.Map

A map maps keys to values. 

The zero value of a map is nil. A nil map has no keys, nor can keys be added. 

The make function returns a map of the given type, initialized and ready for use.

```go
package main

import "fmt"

type Vertex struct {
	Lat, Long float64
}

var m map[string]Vertex

func main() {
	m = make(map[string]Vertex)
	m["Bell Labs"] = Vertex{
		40.68433, -74.39967,
	}
	fmt.Println(m["Bell Labs"])
}
```

**Map literals**

Map literals are like struct literals, but the keys are required.

```go
package main

import "fmt"

type Vertex struct {
	Lat, Long float64
}

var m = map[string]Vertex{
	"Bell Labs": Vertex{
		40.68433, -74.39967,
	},
	"Google": Vertex{
		37.42202, -122.08408,
	},
}

func main() {
	fmt.Println(m)
}
```



**Mutating Maps**

insert or update an element in map m:

```go
m[key] = elem
```



retrieve an element

```go
elem = m[key]
```

delete an element

```go
delete(m,key)
```

test that a key is present with a two-value assignment

```go
elemt,ok = m[key]
```

- If key is in m, ok is true. If not, ok is false. 
- If key is not in the map, then elem is the zero value for the map's element type. 



### 18.Function values

Functions are values too. They can be passed around just like other values. 

Function values may be used as function arguments and return values. 

```go
package main

import (
	"fmt"
	"math"
)

func compute(fn func(float64, float64) float64) float64 {
	return fn(3, 4)
}

func main() {
	hypot := func(x, y float64) float64 {
		return math.Sqrt(x*x + y*y)
	}
	fmt.Println(hypot(5, 12))

	fmt.Println(compute(hypot))
	fmt.Println(compute(math.Pow))
}
```

### 19.Function closures

Go functions may be closures. 

A closure is a function value that references variables from outside its body.

The function may access and assign to the referenced variables

```go
package main

import "fmt"

func adder() func(int) int {
	sum := 0
	return func(x int) int {
		sum += x
		return sum
	}
}

func main() {
	pos, neg := adder(), adder()
	for i := 0; i < 10; i++ {
		fmt.Println(
			pos(i),
			neg(-2*i),
		)
	}
}
```

## Methods and interfaces

### 1.Methods

Go does not have classes. However, you can define methods on types.

A method is a function with a special *receiver* argument.

The receiver appears in its own argument list **between the** **func** **keyword and the method name.**

```go
package main

import (
	"fmt"
	"math"
)

type Vertex struct {
	X, Y float64
}

func (v Vertex) Abs() float64 {
	return math.Sqrt(v.X*v.X + v.Y*v.Y)
}

func main() {
	v := Vertex{3, 4}
	fmt.Println(v.Abs())
}
```


 Remember: **a method is just a function with a receiver argument.**

Here's Abs written as a regular function with no change in functionality.

```go
package main

import (
	"fmt"
	"math"
)

type Vertex struct {
	X, Y float64
}

func Abs(v Vertex) float64 {
	return math.Sqrt(v.X*v.X + v.Y*v.Y)
}

func main() {
	v := Vertex{3, 4}
	fmt.Println(Abs(v))
}
```

You can declare a method on **non-struct types**, too.

```go
package main

import (
	"fmt"
	"math"
)

type MyFloat float64

func (f MyFloat) Abs() float64 {
	if f < 0 {
		return float64(-f)
	}
	return float64(f)
}

func main() {
	f := MyFloat(-math.Sqrt2)
	fmt.Println(f.Abs())
}
```



**Pointer receivers**

```go
package main

import (
	"fmt"
	"math"
)

type Vertex struct {
	X, Y float64
}

// Methods with pointer receivers can modify the value
func (v Vertex) Abs() float64 {
	return math.Sqrt(v.X*v.X + v.Y*v.Y)
}

func (v *Vertex) Scale(f float64) {
	v.X = v.X * f
	v.Y = v.Y * f
}

func main() {
	v := Vertex{3, 4}
    // even though v is a value and not a pointer, the 
    // method with the pointer receiver is called automatically.
    // but common functions that take a value argument must take 
    // a value of that specific type:
	v.Scale(10)
	fmt.Println(v.Abs())
}
```

**Methods with pointer receivers can modify the value** to which the receiver points (as Scale does here). Since methods often need to modify their receiver, **pointer receivers are more common than value receivers**.

It can **avoid copying the value on each method call.** This can be more efficient if the receiver is a large struct, for example.



With a value receiver, the Scale method operates on **a copy of the original** Vertex value. (This is the same behavior as for any other function argument.) The Scale method must have a pointer receiver to change the Vertex value declared in the main function.



### 2.Interfaces

```go
package main

import (
	"fmt"
	"math"
)

type Abser interface {
	Abs() float64
}

func main() {
	var a Abser
	f := MyFloat(-math.Sqrt2)
	v := Vertex{3, 4}

	a = f  // a MyFloat implements Abser
	a = &v // a *Vertex implements Abser

	// In the following line, v is a Vertex (not *Vertex)
	// and does NOT implement Abser.
	a = v

	fmt.Println(a.Abs())
}

type MyFloat float64

// we don't need to explicitly declare that it does so.
func (f MyFloat) Abs() float64 {
	if f < 0 {
		return float64(-f)
	}
	return float64(f)
}

type Vertex struct {
	X, Y float64
}

func (v *Vertex) Abs() float64 {
	return math.Sqrt(v.X*v.X + v.Y*v.Y)
}
```

An *interface type* is defined as a set of method signatures.

A value of interface type can hold any value that implements those methods.



A type implements an interface by implementing its methods. **There is no explicit declaration of intent, no "implements" keyword.**





Under the hood, **interface values can be thought of as a tuple of a value and a concrete type:**

**(value, type)**

```go
package main

import (
	"fmt"
	"math"
)

type I interface {
	M()
}

type T struct {
	S string
}

func (t *T) M() {
	fmt.Println(t.S)
}

type F float64

func (f F) M() {
	fmt.Println(f)
}

func main() {
	var i I

	i = &T{"Hello"}
	describe(i)
	i.M()

	i = F(math.Pi)
	describe(i)
	i.M()
}

func describe(i I) {
	fmt.Printf("(%v, %T)\n", i, i)
}
```



If the concrete value inside the interface itself is nil, the method will be called with a nil receiver.

```go
package main

import "fmt"

type I interface {
	M()
}

type T struct {
	S string
}

func (t *T) M() {
	if t == nil {
		fmt.Println("<nil>")
		return
	}
	fmt.Println(t.S)
}

func main() {
	var i I

	var t *T
	i = t
	describe(i)
	i.M()

	i = &T{"hello"}
	describe(i)
	i.M()
}

func describe(i I) {
	fmt.Printf("(%v, %T)\n", i, i)
}
```

In some languages this would trigger a null pointer exception, but in Go i**t is common to write methods that gracefully handle being called with a nil receiver** (as with the method M in this example.)



A nil interface value holds neither value nor concrete type.Calling a method on a nil interface is a run-time error because there is no type inside the interface tuple to indicate which *concrete* method to call.



**Empty Interface:**



The interface type that specifies zero methods is known as the *empty interface*:

**interface{}**

An empty interface may hold values of any type. (Every type implements at least zero methods.)

```go
package main

import "fmt"

func main() {
	var i interface{}
	describe(i)

	i = 42
	describe(i)

	i = "hello"
	describe(i)
}

func describe(i interface{}) {
	fmt.Printf("(%v, %T)\n", i, i)
}
```

Empty interfaces are used by code that handles values of unknown type. For example, fmt.Print takes any number of arguments of type interface{}.




 **Type assertions**

```go
package main

import "fmt"

func main() {
	var i interface{} = "hello"

    // This statement asserts that the interface value i holds the
    // concrete type T and assigns the underlying T value to the variable t.
	s := i.(string)
	fmt.Println(s)

	s, ok := i.(string)
	fmt.Println(s, ok)

	f, ok := i.(float64)
	fmt.Println(f, ok)

	f = i.(float64) // panic
	fmt.Println(f)
}
```

**Type switches**

```go
package main

import "fmt"

func do(i interface{}) {
	switch v := i.(type) {
	case int:
		fmt.Printf("Twice %v is %v\n", v, v*2)
	case string:
		fmt.Printf("%q is %v bytes long\n", v, len(v))
	default:
		fmt.Printf("I don't know about type %T!\n", v)
	}
}

func main() {
	do(21)
	do("hello")
	do(true)
}
```

A type switch is like a regular switch statement, but the cases in a type switch specify types (not values), and those values are compared against the type of the value held by the given interface value.



**Stringers**

```go
package main

import "fmt"

type Person struct {
	Name string
	Age  int
}

func (p Person) String() string {
	return fmt.Sprintf("%v (%v years)", p.Name, p.Age)
}

func main() {
	a := Person{"Arthur Dent", 42}
	z := Person{"Zaphod Beeblebrox", 9001}
	fmt.Println(a, z)
}
```

One of the most ubiquitous interfaces is [Stringer](https://golang.google.cn/pkg/fmt/#Stringer) defined by the [fmt](https://golang.google.cn/pkg/fmt/) package.

type Stringer interface {  String() string }

A Stringer is a type that can describe itself as a string. The fmt package (and many others) look for this interface to print values.

### 3.Errors

Go programs express error state with error values.

The error type is a built-in interface similar to fmt.Stringer:

```go
type error interface {
    Error() string 
}
```

(As with fmt.Stringer, the fmt package looks for the error interface when printing values.)

Functions often return an error value, and calling code should handle errors by testing whether the error equals nil.

```go
i, err := strconv.Atoi("42")
if err != nil {
    fmt.Printf("couldn't convert number: %v\n", err)
    return
}
fmt.Println("Converted integer:", i)
```

A nil error denotes success; a non-nil error denotes failure.

```go
package main

import (
	"fmt"
	"time"
)

type MyError struct {
	When time.Time
	What string
}

func (e *MyError) Error() string {
	return fmt.Sprintf("at %v, %s",
		e.When, e.What)
}

func run() error {
	return &MyError{
		time.Now(),
		"it didn't work",
	}
}

func main() {
	if err := run(); err != nil {
		fmt.Println(err)
	}
}
```

### 4.Readers

The io package specifies the io.Reader interface, which represents the read end of a stream of data.

The Go standard library contains [many implementations](https://cs.opensource.google/search?q=Read\(\w%2B\s\[\]byte\)&ss=go%2Fgo) of this interface, including files, network connections, compressors, ciphers, and others.

The io.Reader interface has a Read method:

```go
func (T) Read(b []byte) (n int, err error)
```

Read populates the given byte slice with data and returns the number of bytes populated and an error value. It returns an io.EOF error when the stream ends.

The example code creates a [strings.Reader](https://golang.google.cn/pkg/strings/#Reader) and consumes its output 8 bytes at a time.

```go
package main

import (
	"fmt"
	"io"
	"strings"
)

func main() {
	r := strings.NewReader("Hello, Reader!")

	b := make([]byte, 8)
	for {
		n, err := r.Read(b)
		fmt.Printf("n = %v err = %v b = %v\n", n, err, b)
		fmt.Printf("b[:n] = %q\n", b[:n])
		if err == io.EOF {
			break
		}
	}
}
```

## Generics

### 1.Type parameter

Go functions can be written to work on multiple types using type parameters. The type parameters of a function appear between brackets, before the function's arguments.

```go
package main

import "fmt"

// Index returns the index of x in s, or -1 if not found.
func Index[T comparable](s []T, x T) int {
	for i, v := range s {
		// v and x are type T, which has the comparable
		// constraint, so we can use == here.
		if v == x {
			return i
		}
	}
	return -1
}

func main() {
	// Index works on a slice of ints
	si := []int{10, 20, 15, -10}
	fmt.Println(Index(si, 15))

	// Index also works on a slice of strings
	ss := []string{"foo", "bar", "baz"}
	fmt.Println(Index(ss, "hello"))
}
```

This declaration means that s is a slice of any type T that fulfills the built-in constraint comparable. x is also a value of the same type.



comparable is a useful constraint that makes it possible to use the == and != operators on values of the type. In this example, we use it to compare a value to all slice elements until a match is found. This Index function works for any type that supports comparison.

### 3.Generic types

```go
package main

// List represents a singly-linked list that holds
// values of any type.
type List[T any] struct {
	next *List[T]
	val  T
}

func main() {
}
```

Go also supports generic types. A type can be parameterized with a type parameter, which could be useful for implementing generic data structures.



## Concurrency

### 1.Goroutines

A *goroutine* is a lightweight thread managed by the Go runtime.

```go
package main

import (
	"fmt"
	"time"
)

func say(s string) {
	for i := 0; i < 5; i++ {
		time.Sleep(100 * time.Millisecond)
		fmt.Println(s)
	}
}

func main() {
    // starts a new goroutine running
	go say("world")
	say("hello")
}
```

### 2.Channels

Channels are a typed conduit through which you can send and receive values with the channel operator, <-.

```go
ch <- v    // Send v to channel ch.
v := <-ch  // Receive from ch, and assign value to v.
```

By default, sends and receives block until the other side is ready. This allows goroutines to synchronize without explicit locks or condition variables.

```go
package main

import "fmt"

func sum(s []int, c chan int) {
	sum := 0
	for _, v := range s {
		sum += v
	}
    
	c <- sum // send sum to c
}

func main() {
	s := []int{7, 2, 8, -9, 4, 0}

	c := make(chan int)
	go sum(s[:len(s)/2], c)
	go sum(s[len(s)/2:], c)
	x, y := <-c, <-c // receive from c

	fmt.Println(x, y, x+y)
}
```

### 3.Buffered Channels

Channels can be *buffered*. Provide the buffer length as the second argument to make to initialize a buffered channel:

```go
package main

import "fmt"

func main() {
	ch := make(chan int, 2)
	ch <- 1
	ch <- 2
	fmt.Println(<-ch)
	fmt.Println(<-ch)
}
```

Sends to a buffered channel block only when the buffer is full. Receives block when the buffer is empty.

### 4.Range and Close

A sender can **close** **a channel to indicate that no more values will be sent**. Receivers can **test whether a channel has been closed** by assigning a second parameter to the receive expression: after

```go
v, ok := <-ch
```

ok is false if there are no more values to receive and the channel is closed.

```go
package main

import (
	"fmt"
)

func fibonacci(n int, c chan int) {
	x, y := 0, 1
	for i := 0; i < n; i++ {
		c <- x
		x, y = y, x+y
	}
	close(c)
}

func main() {
	c := make(chan int, 10)
	go fibonacci(cap(c), c)
    // The loop for i := range c receives values from the channel 
    // repeatedly until it is closed.
	for i := range c {
		fmt.Println(i)
	}
}
```

**Note:** Only the sender should close a channel, never the receiver. Sending on a closed channel will cause a panic.

**Another note:** Channels aren't like files; you don't usually need to close them. Closing is only necessary when the receiver must be told there are no more values coming, such as to terminate a range loop.

### 5.Select

The select statement lets a goroutine wait on multiple communication operations.

A select blocks until one of its cases can run, then it executes that case. It chooses one at random if multiple are ready.

```go
package main

import "fmt"

func fibonacci(c, quit chan int) {
	x, y := 0, 1
	for {
		select {
		case c <- x:
			x, y = y, x+y
		case <-quit:
			fmt.Println("quit")
			return
		}
	}
}

func main() {
	c := make(chan int)
	quit := make(chan int)
	go func() {
		for i := 0; i < 10; i++ {
			fmt.Println(<-c)
		}
		quit <- 0
	}()
	fibonacci(c, quit)
}
```

while,The default case in a select is run if no other case is ready.

```go
package main

import (
	"fmt"
	"time"
)

func main() {
	tick := time.Tick(100 * time.Millisecond)
	boom := time.After(500 * time.Millisecond)
	for {
		select {
		case <-tick:
			fmt.Println("tick.")
		case <-boom:
			fmt.Println("BOOM!")
			return
		default:
			fmt.Println("    .")
			time.Sleep(50 * time.Millisecond)
		}
	}
}
```

### 6.sync.Mutex

We've seen how channels are great for communication among goroutines.

But what if we don't need communication? What if we just want to make sure only one goroutine can access a variable at a time to avoid conflicts?

This concept is called *mutual exclusion*, and the conventional name for the data structure that provides it is *mutex*.

Go's standard library provides mutual exclusion with [sync.Mutex](https://golang.google.cn/pkg/sync/#Mutex) and its two methods:

- Lock
- Unlock

We can define a block of code to be executed in mutual exclusion by surrounding it with a call to Lock and Unlock as shown on the Inc method.

We can also use defer to ensure the mutex will be unlocked as in the Value method

```go
package main

import (
	"fmt"
	"sync"
	"time"
)

// SafeCounter is safe to use concurrently.
type SafeCounter struct {
	mu sync.Mutex
	v  map[string]int
}

// Inc increments the counter for the given key.
func (c *SafeCounter) Inc(key string) {
	c.mu.Lock()
	// Lock so only one goroutine at a time can access the map c.v.
	c.v[key]++
	c.mu.Unlock()
}

// Value returns the current value of the counter for the given key.
func (c *SafeCounter) Value(key string) int {
	c.mu.Lock()
	// Lock so only one goroutine at a time can access the map c.v.
	defer c.mu.Unlock()
	return c.v[key]
}

func main() {
	c := SafeCounter{v: make(map[string]int)}
	for i := 0; i < 1000; i++ {
		go c.Inc("somekey")
	}

	time.Sleep(time.Second)
	fmt.Println(c.Value("somekey"))
}
```