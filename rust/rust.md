# 一.Cargo

## 1.创建项目

cargo new 项目名称

帮助信息

```plain
Create a new cargo package at <path>

Usage: cargo.exe new [OPTIONS] <path>

Arguments:
  <path>

Options:
  -q, --quiet                Do not print cargo log messages
      --registry <REGISTRY>  Registry to use
      --vcs <VCS>            Initialize a new repository for the given version control system (git, hg, pijul, or fossil) or do not initialize any version control at all (none), overriding a global configuration. [possible values: git, hg, pijul, fossil, none]
      --bin                  Use a binary (application) template [default]
  -v, --verbose...           Use verbose output (-vv very verbose/build.rs output)
      --lib                  Use a library template
      --color <WHEN>         Coloring: auto, always, never
      --edition <YEAR>       Edition to set for the crate generated [possible values: 2015, 2018, 2021]
      --frozen               Require Cargo.lock and cache are up to date
      --name <NAME>          Set the resulting package name, defaults to the directory name
      --locked               Require Cargo.lock is up to date
      --offline              Run without accessing the network
      --config <KEY=VALUE>   Override a configuration value
  -Z <FLAG>                  Unstable (nightly-only) flags to Cargo, see 'cargo -Z help' for details
  -h, --help                 Print help information

Run `cargo help new` for more detailed information.
```

**Cargo.toml**

TOML(Tom's Obvious,Minimal Language)格式，是Cargo的配置格式

```plain
[package]#区域标题，表示一下面是用来配置包package的
name = "hello" #项目名称
version = "0.1.0" #项目版本
authors = ["cauchy <731005515@qq.com>"] #作者
edition = "2021" #使用的Rust版本

# See more keys and their definitions at https://doc.rust-lang.org/cargo/reference/manifest.html

[dependencies]#依赖项
```

在Rust中，代码的包叫做**crate**

## 2.构建Cargo项目

cargo build

创建可执行文件target/debug/hello_cargo或target\debug\hello_cargo.exe(Windows)

运行.\target\debug\hello_cargo.exe

第一次运行会生成cargo.lock文件

该文件负责追踪项目依赖的精确版本，不需要手动修改该文件

## 3.构建和运行Cargo项目

cargo run

如果之前编译过且代码没有修改的话会直接执行

## 4.cargo check

cargo check

检查代码，确保能通过编译，但是不产生任何可执行文件

cargo check比cargo build快得多

## 5.发布构建

cargo build --release

编译时会进行优化，代码运行的更快但是编译时间更长

会在target/release而不是target/debug生成可执行文件

# 二.猜数游戏

```plain
use rand::Rng;
use std::cmp::Ordering;
use std::io;
fn main() {
    println!("猜数");
    println!("猜一个数");
    let secret_number = rand::thread_rng().gen_range(1..101);
    println!("secret_number: {}", secret_number);

    loop {
        let mut guess = String::new();
        io::stdin().read_line(&mut guess).expect("无法运行");
        println!("你猜测的数是{}", guess);
        //shadow
        let guess: u32 = match guess.trim().parse(){
            Ok(num)=>num,
            Err(_)=>continue
        };
        match guess.cmp(&secret_number) {
            Ordering::Less => println!("too small!"),
            Ordering::Greater => println!("too big!"),
            Ordering::Equal => {
                println!("you win!");
                break;
            },
        }
    }
}
```

# 三.变量与可变性

### **变量**

声明使用**let**关键字

默认情况下，变量是不可变的(immutable)

声明变量时，前面加上**mut**关键字，就可以使变量可变

let mut x = 3;

### **常量**

类似于不可变变量，*常量(constants)* 是绑定到一个名称的不允许改变的值，不过常量与变量还是有一些区别。

1.不允许对常量使用 mut。常量不光默认不能变，它总是不能变。

2.声明常量使用 const 关键字而不是 let，并且 ***必须*** **注明值的类型**。

3.常量可以在任何作用域中声明，包括全局作用域，

4.最后一个区别是，常量只能被设置为**常量表达式**，而**不可以是其他任何只能在运行时计算出的值**。

```plain
const THREE_HOURS_IN_SECONDS: u32 = 60 * 60 * 3;
const MAX_POINTS:u32 = 100_000;
```

命名规范:全部大写，下划线间隔

### 隐藏(shadow)

```plain
fn main() {
    let x = 5;

    let x = x + 1;

    {
        let x = x * 2;
        println!("The value of x in the inner scope is: {x}");
    }

    println!("The value of x is: {x}");
}
```

我们可以定义一个与之前变量同名的新变量,**新的变量会shadow之前声明的同名变量**

shadow和把变量标记为mut**是不一样的**

- 如果不适用let关键字，那么给非mut的变量赋值会导致编译时错误
- 使用let声明的同名新变量，也是不可变的
- 使用let声明的同名新变量，**他的类型可以与之前不同**

### 允许未使用的变量

两种方式

```plain
fn main() {
    let _x = 1;
}
#[allow(unused_variables)]
fn main() {
    let x = 1;
}
```

# 四.数据类型

Rust是静态编译语言，编译时必须知道所有变量的类型

## 标量类型

Rust 有四种基本的标量类型：整型、浮点型、布尔类型和字符类型

### 整数类型

如果我们没有显式的给予变量一个类型，那编译器会自动帮我们推导一个类型

```plain
fn main(){
    let x = 5;
    assert_eq!("i32".to_string(),type_of(&x));
}
// 以下函数可以获取传入参数的类型，并返回类型的字符串形式
fn type_of<T>(_: &T) -> String{
    format!("{}",std::any::type_name::<T>())
}
```

整数如果不赋予类型默认为i32类型

```plain
fn main() {
    let v: u16 = 38_u8 as u16;
}
```

| **长度** | **有符号** | **无符号** |
| -------- | ---------- | ---------- |
| 8-bit    | i8         | u8         |
| 16-bit   | i16        | u16        |
| 32-bit   | i32        | u32        |
| 64-bit   | i64        | u64        |
| 128-bit  | i128       | u128       |
| arch     | isize      | usize      |

isize 和 usize 类型依赖运行程序的计算机架构：64 位架构上它们是 64 位的， 32 位架构上它们是 32 位的。

```plain
fn main() {
    assert_eq!(i8::MAX, 127); 
    assert_eq!(u8::MAX, 255); 
}
```

**整型字面值**

| **数字字面值**              | **例子**    |
| --------------------------- | ----------- |
| Decimal (十进制)            | 98_222      |
| Hex (十六进制)              | 0xff        |
| Octal (八进制)              | 0o77        |
| Binary (二进制)             | 0b1111_0000 |
| Byte (单字节字符)(仅限于u8) | b'A'        |

Rust 的数字类型默认是 i32。isize 或 usize 主要作为某些集合的索引。

**整形溢出**

比方说有一个 u8 ，它可以存放从零到 255 的值。那么当你将其修改为 256 时会发生什么呢？这被称为 “整型溢出”（“integer overflow” ），这会导致以下两种行为之一的发生。当在 debug 模式编译时，Rust 检查这类问题并使程序 *panic*，这个术语被 Rust 用来表明程序因错误而退出。

在 release 构建中，Rust 不检测溢出，相反会进行一种被称为二进制补码回绕（*two’s complement wrapping*）的操作。简而言之，比此类型能容纳最大值还大的值会回绕到最小值，值 256 变成 0，值 257 变成 1，依此类推。依赖整型回绕被认为是一种错误，即便可能出现这种行为。如果你确实需要这种行为，标准库中有一个类型显式提供此功能，Wrapping。 为了显式地处理溢出的可能性，你可以使用标准库在原生数值类型上提供的以下方法:

- 所有模式下都可以使用 wrapping_* 方法进行回绕，如 wrapping_add
- 如果 checked_* 方法出现溢出，则返回 None值
- 用 overflowing_* 方法返回值和一个布尔值，表示是否出现溢出
- 用 saturating_* 方法在值的最小值或最大值处进行饱和处理

```plain
// 解决代码中的错误和 `panic`
fn main() {
   let v1 = 251_u8 + 8;
   let v2 = i8::checked_add(251, 8).unwrap();
   println!("{},{}",v1,v2);
}
```

修改

```plain
fn main() {
    let v1 = 247_u8 + 8;
    let v2 = i8::checked_add(119, 8).unwrap();
    println!("{},{}",v1,v2);
 }
#[allow(unused_variables)]
fn main() {
    let v1 = 251_u16 + 8;
    let v2 = u16::checked_add(251, 8).unwrap();
    println!("{},{}",v1,v2);
 }
```

### 浮点类型

Rust 的浮点数类型是 f32 和 f64，分别占 32 位和 64 位。默认类型是 f64，因为在现代 CPU 中，它与 f32 速度几乎一样，不过精度更高。所有的浮点型都是有符号的。

```plain
fn main() {
    let x = 2.0; // f64

    let y: f32 = 3.0; // f32
}
```

浮点数采用 IEEE-754 标准表示。f32 是单精度浮点数，f64 是双精度浮点数。

```plain
fn main() {
    let x = 1_000.000_1; // f64
    let y: f32 = 0.12; // f32
    let z = 0.01_f64; // f64
}
```

**数值运算**

Rust 中的所有数字类型都支持基本数学运算：加法、减法、乘法、除法和取余。整数除法会**向下舍入**到最接近的整数

```plain
fn main() {
    assert_eq!(0.1+0.2,0.3);//报错
 }
thread 'main' panicked at 'assertion failed: `(left == right)`
  left: `0.30000000000000004`,
 right: `0.3`', src\main.rs:5:5
```

两种修改方法

```plain
fn main() {
    assert!(0.1+0.2>=0.3);
}
fn main() {
    assert!(0.1_f32+0.2_f32==0.3_f32);
}
```

**计算**

```plain
use std::fmt::Display;

#[allow(unused_variables)]
fn print_something<T >(something:T)
where T : Display
{
    println!("{}",something);
}
fn main() {
    // 整数加法
    print_something(1u32 + 2  );

    // 整数减法
    print_something(1i32 - 2  );
    print_something(1i8 - 2);
    
    print_something(3 * 50  );

    print_something(9 / 3 == 3); // error ! 修改它让代码工作

    print_something(24 % 5  );
    
    // 逻辑与或非操作
    print_something(true && false  );
    print_something(true || false  );
    print_something(!true  );

    // 位操作
    println!("0011 AND 0101 is {:04b}", 0b0011u32 & 0b0101);
    println!("0011 OR 0101 is {:04b}", 0b0011u32 | 0b0101);
    println!("0011 XOR 0101 is {:04b}", 0b0011u32 ^ 0b0101);
    println!("1 << 5 is {}", 1u32 << 5);
    println!("0x80 >> 2 is 0x{:x}", 0x80u32 >> 2);
}
```

### 序列

```plain
fn main() {
    let mut sum = 0;
    for i in -3..2 {
        println!("i is {}",i);//-3到1不包括2
    }

    for c in 'a'..='z' {
        println!("{}",c);//a-z包括z
    }
}
#[allow(unused_variables)]
// 解决代码中的错误和 `panic`
use std::ops::{Range, RangeInclusive};
fn main() {
    assert_eq!((1..5), Range{ start: 1, end: 5 });
    assert_eq!((1..=5), RangeInclusive::new(1, 5));
}
```

### 布尔类型

Rust 中的布尔类型使用 bool 表示

```plain
fn main() {
    let t = true;

    let f: bool = false; // with explicit type annotation
}
fn main() {
    let f = true;
    let t = true && false || true;//布尔运算
    assert_eq!(t, f);

    println!("Success!")
}
```

### 字符类型

Rust的 char 类型是语言中最原生的字母类型

```plain
fn main() {
    let c = 'z';
    let z: char = 'ℤ'; // with explicit type annotation
    let heart_eyed_cat = '😻';
}
fn main() {
    let c1 = '中';
    print_char(c1);
} 

fn print_char(c : char) {
    println!("{}", c);
}
```

用单引号声明 char 字面量，而与之相反的是，使用双引号声明字符串字面量。Rust 的 char 类型的大小为**四个字节**(four bytes)，并代表了一个 Unicode 标量值（Unicode Scalar Value），这意味着它可以比 ASCII 表示更多内容。在 Rust 中，带变音符号的字母（Accented letters），中文、日文、韩文等字符，emoji（绘文字）以及零长度的空白字符都是有效的 char 值。Unicode 标量值包含从 U+0000 到 U+D7FF 和 U+E000 到 U+10FFFF 在内的值。不过，“字符” 并不是一个 Unicode 中的概念，所以人直觉上的 “字符” 可能与 Rust 中的 char 并不符合。

**大小**

```plain
#[allow(unused_variables)]

use std::mem::size_of_val;
fn main() {
    let c1 = 'a';
    assert_eq!(size_of_val(&c1),4); //一个字符4个字节

    let c2 = '中';
    assert_eq!(size_of_val(&c2),4); 

    println!("Success!")
}
```

### 单元类型

```plain
fn main() {
    let _v: () = ();

    let v = (2, 3);
    assert_eq!(_v, implicitly_ret_unit());

    println!("Success!")
}

fn implicitly_ret_unit() {
    println!("I will return a ()")
}
```

**单元类型所占的内存为0！！！**

```plain
use std::mem::size_of_val;
fn main() {
    let unit: () = ();
    assert!(size_of_val(&unit) == 0);

    println!("Success!")
}
```

## 复合类型

**复合类型**（*Compound types*）可以将多个值组合成一个类型。Rust 有两个原生的复合类型：元组（tuple）和数组（array）。

### 元组类型

元组**长度固定**：一旦声明，其长度不会增大或缩小

使用包含在圆括号中的逗号分隔的值列表来创建一个元组。元组中的每一个位置都有一个类型，而且这些不同值的**类型也不必是相同的**

```plain
fn main() {
    let tup: (i32, f64, u8) = (500, 6.4, 1);
}
```

tup 变量绑定到整个元组上，因为元组是一个单独的复合元素。为了从元组中获取单个值，可以使用**模式匹配**（pattern matching）来**解构**（destructure）元组值，像这样：

```plain
fn main() {
    let tup = (500, 6.4, 1);

    let (x, y, z) = tup;

    println!("The value of y is: {y}");
}
fn main() {
    let (x, y, z);

    // 填空
    (y,z,x) = (1, 2, 3);
    
    assert_eq!(x, 3);
    assert_eq!(y, 1);
    assert_eq!(z, 2);
}
```

程序首先创建了一个元组并绑定到 tup 变量上。接着使用了 let 和一个模式将 tup 分成了三个不同的变量，x、y 和 z。这叫做 **解构**（*destructuring*），因为它将一个元组拆成了三个部分。

也可以使用点号（.）后跟值的索引来直接访问它们。元组的第一个索引值是 0。例如：

```plain
fn main() {
    let x: (i32, f64, u8) = (500, 6.4, 1);

    let five_hundred = x.0;

    let six_point_four = x.1;

    let one = x.2;
}
```

不带任何值的元组有个特殊的名称，叫做 **单元（unit）** 元组。这种值以及对应的类型都写作 ()，表示空值或空的返回类型。如果表达式不返回任何其他值，则会隐式返回单元值。

**过长的元组无法打印**

```plain
// 修复代码错误
fn main() {
    let too_long_tuple = (1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13);
    println!("too long tuple: {:?}", too_long_tuple);
}
//修复
fn main() {
    let too_long_tuple = (1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12);
    println!("too long tuple: {:?}", too_long_tuple);
}
```

### 数组类型

与元组不同，数组中的每个元素的类型必须相同。Rust 中的数组与一些其他语言中的数组不同，Rust中的数组长度是固定的。

**数组的类型是** **[T; Length]****，数组的长度是类型签名的一部分，因此数组的长度必须在编译期就已知，**

vector 类型是标准库提供的一个 允许 增长和缩小长度的类似数组的集合类型。当不确定是应该使用数组还是 vector 的时候，那么很可能应该使用 vector。

可以像这样编写数组的类型：在方括号中包含每个元素的类型，后跟分号，再后跟数组元素的数量。

let a: [i32; 5] = [1, 2, 3, 4, 5];

这里，i32 是每个元素的类型。分号之后，数字 5 表明该数组包含五个元素。

```plain
fn main() {
    // 很多时候，我们可以忽略数组的部分类型，也可以忽略全部类型，让编译器帮助我们推导
    let arr0 = [1, 2, 3];
    let arr: [char; 3] = ['a', 'b', 'c'];
    
    // 数组分配在栈上， `std::mem::size_of_val` 函数会返回整个数组占用的内存空间
    // 数组中的每个 char 元素占用 4 字节的内存空间，因为在 Rust 中， char 是 Unicode 字符
    assert!(std::mem::size_of_val(&arr) == 12);
}
```

还可以通过在方括号中指定初始值加分号再加元素个数的方式来创建一个**每个元素都为相同值的数组**：

let a = [3; 5];

变量名为 a 的数组将包含 5 个元素，这些元素的值最初都将被设置为 3。这种写法与 let a = [3, 3, 3, 3, 3]; 效果相同，但更简洁。

**访问数组元素**

数组是可以在栈(stack)上分配的已知固定大小的单个内存块。可以使用索引来访问数组的元素，像这样：

```plain
fn main() {
    let a = [1, 2, 3, 4, 5];

    let first = a[0];
    let second = a[1];
}
fn main() {
    let names = [String::from("Sunfei"), "Sunface".to_string()];
    
    // `get` 返回 `Option<T>` 类型，因此它的使用非常安全
    let name0 = names.get(0).unwrap();

    // 但是下标索引就存在越界的风险了
    let _name1 = &names[1];
}
```

**无效的数组访问**

如果我们访问数组结尾之后的元素，程序在索引操作中使用一个无效的值时导致 **运行时** 错误。程序带着错误信息退出。当尝试用索引访问一个元素时，Rust 会检查指定的索引是否小于数组的长度。如果索引超出了数组长度，Rust 会 *panic*，这是 Rust 术语，它用于程序因为错误而退出的情况。这种检查必须在运行时进行，特别是在某些情况下，因为编译器不可能知道用户在以后运行代码时将输入什么值。

## 类型转换

### 使用as进行基本类型转换

1.Rust 并没有为基本类型提供隐式的类型转换( coercion )，但是我们可以通过 as 来进行显式地转换。

```plain
#[allow(unused_variables)]
fn main() {
  let decimal = 97.123_f32;

  let integer: u8 = decimal as u8;

  let c1: char = decimal as u8 as char;
  let c2 = integer as char;

  println!("c1 is {}",c1);
  assert_eq!(integer, 'b' as u8 - 1);

  println!("Success!")
}
```

2.默认情况下, 数值溢出会导致编译错误，但是我们可以通过添加一行全局注解的方式来避免编译错误(溢出还是会发生)

```plain
#![allow(overflowing_literals)]
fn main() {
    assert_eq!(u8::MAX, 255);
    let v = 1000 as u8;
}
```

3.当将任何数值转换成无符号整型 T 时，如果当前的数值不在新类型的范围内，我们可以对当前数值进行加值或减值操作( 增加或减少 T::MAX + 1 )，直到最新的值在新类型的范围内，假设我们要将 300 转成 u8 类型，由于u8 最大值是 255，因此 300 不在新类型的范围内并且大于新类型的最大值，因此我们需要减去 T::MAX + 1，也就是 300 - 256 = 44。

```plain
#![allow(overflowing_literals)]
fn main() {
  assert_eq!(1000 as u16, 1000);

  assert_eq!(1000 as u8, 232);

  // 事实上，之前说的规则对于正整数而言，就是如下的取模
  println!("1000 mod 256 is : {}", 1000 % 256);

  assert_eq!(-1_i8 as u8, 255);
  
   // 从 Rust 1.45 开始，当浮点数超出目标整数的范围时，转化会直接取正整数取值范围的最大或最小值
  assert_eq!(300.1_f32 as u8, 255);
  assert_eq!(-100.1_f32 as u8, 0);
  

    // 上面的浮点数转换有一点性能损耗，如果对于某段代码有极致的性能要求，
    // 可以考虑下面的方法，但是这些方法的结果可能会溢出并且返回一些无意义的值
    // 总之，请小心使用
  unsafe {
      // 300.0 is 44
      println!("300.0 is {}", 300.0_f32.to_int_unchecked::<u8>());
      // -100.0 as u8 is 156
      println!("-100.0 as u8 is {}", (-100.0_f32).to_int_unchecked::<u8>());
      // nan as u8 is 0
      println!("nan as u8 is {}", f32::NAN.to_int_unchecked::<u8>());
  }
}
```

4.裸指针可以和代表内存地址的整数互相转换

```plain
fn main() {
    let mut values: [i32; 2] = [1, 2];
    let p1: *mut i32 = values.as_mut_ptr();
    let first_address = p1 as usize; 
    let second_address = first_address + 4; // 4 == std::mem::size_of::<i32>()
    let p2 = second_address as *mut i32;
    unsafe {
        *p2 += 1;
    }
    assert_eq!(values[1], 3);

    println!("Success!")
}
fn main() {
    let arr :[u64; 13] = [0; 13];
    assert_eq!(std::mem::size_of_val(&arr), 8 * 13);
    let a: *const [u64] = &arr;
    let b = a as *const [u8];
    unsafe {
        assert_eq!(std::mem::size_of_val(&*b), 13)
    }
}
```

### From/Into

1. From 特征允许让一个类型定义如何基于另一个类型来创建自己，因此它提供了一个很方便的类型转换的方式。
2. From 和 Into 是配对的，我们只要实现了前者，那后者就会自动被实现：只要实现了 impl From<T> for U， 就可以使用以下两个方法: let u: U = U::from(T) 和 let u:U = T.into()，前者由 From 特征提供，而后者由自动实现的 Into 特征提供。
3. 需要注意的是，当使用 into 方法时，需要进行显式地类型标注，因为编译器很可能无法帮我们推导出所需的类型。

例子

```plain
fn main() {
    let my_str = "hello";

    // 以下三个转换都依赖于一个事实：String 实现了 From<&str> 特征
    let string1 = String::from(my_str);
    let string2 = my_str.to_string();
    // 这里需要显式地类型标注
    let string3: String = my_str.into();
}
fn main() {
     // impl From<bool> for i32
    let i1:i32 = false.into();
    let i2:i32 = i32::from(false);  
    assert_eq!(i1, i2);
    assert_eq!(i1, 0);

    // 使用两种方式修复错误
    // 1. 哪个类型实现 From 特征 : impl From<char> for ? , 你可以查看一下之前提到的文档，来找到合适的类型
    // 2. 上一章节中介绍过的某个关键字
    let i3: i32 = 'a'.into();

    // 使用两种方法来解决错误
    let s: String = 'a' as String;

    println!("Success!")
}

//第一种方法
fn main() {
  // impl From<bool> for i32
 let i1:i32 = false.into();
 let i2:i32 = i32::from(false);  
 assert_eq!(i1, i2);
 assert_eq!(i1, 0);

 let i3:u32 = 'a'.into();

 let s: String = 'a'.into();

 println!("Success!")
}

//第二种方法
fn main() {
     // impl From<bool> for i32
    let i1:i32 = false.into();
    let i2:i32 = i32::from(false);  
    assert_eq!(i1, i2);
    assert_eq!(i1, 0);

    let i3: u32 = 'a' as u32 ;

    let s: String = String::from('a');
}
```

#### 为自定义类型实现 From 特征

```plain
// From 被包含在 `std::prelude` 中，因此我们没必要手动将其引入到当前作用域来
// use std::convert::From;

#[derive(Debug)]
struct Number {
    value: i32,
}

impl From<i32> for Number {
    // 实现 `from` 方法
    fn from(item: i32) -> Self {
        Number { value: item }
    }
}

// 填空
fn main() {
    let num = Number::from(30);
    assert_eq!(num.value, 30);

    let num: Number = 30.into();
    assert_eq!(num.value, 30);

    println!("Success!")
}
```

当执行错误处理时，为我们自定义的错误类型实现 From 特征是非常有用。这样就可以通过 ? 自动将某个错误类型转换成我们自定义的错误类型

```plain
use std::fs;
use std::io;
use std::num;

enum CliError {
    IoError(io::Error),
    ParseError(num::ParseIntError),
}

impl From<io::Error> for CliError {
    fn from(error: io::Error) -> Self {
        CliError::IoError(error)
    }
}

impl From<num::ParseIntError> for CliError {
    fn from(error: num::ParseIntError) -> Self {
        CliError::ParseError(error)
    }
}

fn open_and_parse_file(file_name: &str) -> Result<i32, CliError> {
    // ? automatically converts io::Error to CliError
    let contents = fs::read_to_string(&file_name)?;
    // num::ParseIntError -> CliError
    let num: i32 = contents.trim().parse()?;
    Ok(num)
}

fn main() {
    println!("Success!")
}
```

### TryFrom / TryInto

类似于 From 和 Into, TryFrom 和 TryInto 也是用于类型转换的泛型特征。

但是又与 From/Into 不同, TryFrom 和 TryInto 可以对转换后的失败进行处理，然后返回一个 Result。

```plain
fn main() {
  let n: i16 = 256;

  // Into 特征拥有一个方法`into`,
  // 因此 TryInto 有一个方法是 ?
  let n: u8 = match n.try_into() {
      Ok(n) => n,
      Err(e) => {
          println!("there is an error when converting: {:?}, but we catch it", e.to_string());
          0
      }
  };

  assert_eq!(n, 0);

  println!("Success!")
}
```

自定义实现

```plain
#[derive(Debug, PartialEq)]
struct EvenNum(i32);

impl TryFrom<i32> for EvenNum {
    type Error = ();

    // 实现 `try_from`
    fn try_from(value: i32) -> Result<Self, Self::Error> {
        if value % 2 == 0 {
            Ok(EvenNum(value))
        } else {
            Err(())
        }
    }
}

fn main() {
    assert_eq!(EvenNum::try_from(8), Ok(EvenNum(8)));
    assert_eq!(EvenNum::try_from(5), Err(()));

    // 填空
    let result: Result<EvenNum, ()> = 8i32.try_into();
    assert_eq!(result, Ok(EvenNum(8)));
    let result: Result<EvenNum, ()> = 5i32.try_into();
    assert_eq!(result,Err(()));

    println!("Success!")
}
```

### 其它转换

#### 将任何类型转换成String

只要为一个类型实现了 ToString，就可以将任何类型转换成 String。事实上，这种方式并不是最好的，大家还记得 fmt::Display 特征吗？它可以控制一个类型如何打印，在实现它的时候还会自动实现 ToString。

```plain
use std::fmt;

struct Point {
    x: i32,
    y: i32,
}

impl fmt::Display for Point {
    fn fmt(&self, f: &mut fmt::Formatter<'_>) -> fmt::Result {
        write!(f, "The point is ({}, {})", self.x, self.y)
    }
}

fn main() {
    let origin = Point { x: 0, y: 0 };
    assert_eq!(origin.to_string(), "The point is (0, 0)");
    assert_eq!(format!("{}", origin), "The point is (0, 0)");

    println!("Success!")
}
```

#### 解析String

使用 parse 方法可以将一个 String 转换成 i32 数字，这是因为在标准库中为 i32 类型实现了 FromStr: : impl FromStr for i32

```plain
// To use `from_str` method, you needs to introduce this trait into the current scope.
use std::str::FromStr;
fn main() {
    let parsed: i32 = "5".parse().unwrap();
    let turbo_parsed = "10".parse::<i32>().unwrap();
    let from_str = i32::from_str("20").unwrap();
    let sum = parsed + turbo_parsed + from_str;
    assert_eq!(sum, 35);

    println!("Success!")
}
```

#### 自定义实现FromStr特征

```plain
use std::str::FromStr;
use std::num::ParseIntError;

#[derive(Debug, PartialEq)]
struct Point {
    x: i32,
    y: i32
}

impl FromStr for Point {
    type Err = ParseIntError;

    fn from_str(s: &str) -> Result<Self, Self::Err> {
        let coords: Vec<&str> = s.trim_matches(|p| p == '(' || p == ')' )
                                 .split(',')
                                 .collect();

        let x_fromstr = coords[0].parse::<i32>()?;
        let y_fromstr = coords[1].parse::<i32>()?;

        Ok(Point { x: x_fromstr, y: y_fromstr })
    }
}
fn main() {
    let p = "(3,4)".parse::<Point>();
    assert_eq!(p.unwrap(), Point{ x: 3, y: 4} )
}
```

#### transmute

std::mem::transmute 是一个 unsafe 函数，可以把一个类型按位解释为另一个类型，其中这两个类型必须有同样的位数( bits )。

transmute 相当于将一个类型按位移动到另一个类型，它会将源值的所有位拷贝到目标值中，然后遗忘源值。该函数跟 C 语言中的 memcpy 函数类似。

正因为此，**transmute** **非常非常不安全!** 调用者必须要自己保证代码的安全性，当然这也是 unsafe 的目的。

**示例**

1.transmute 可以将一个指针转换成一个函数指针，该转换并不具备可移植性，原因是在不同机器上，函数指针和数据指针可能有不同的位数( size )。

```plain
fn foo() -> i32 {
    0
}

fn main() {
    let pointer = foo as *const ();
    let function = unsafe {
        std::mem::transmute::<*const (), fn() -> i32>(pointer)
    };
    assert_eq!(function(), 0);
}
```

2.transmute 还可以扩展或缩短一个不变量的生命周期，将 Unsafe Rust 的不安全性体现的淋漓尽致!

```plain
struct R<'a>(&'a i32);
unsafe fn extend_lifetime<'b>(r: R<'b>) -> R<'static> {
    std::mem::transmute::<R<'b>, R<'static>>(r)
}

unsafe fn shorten_invariant_lifetime<'b, 'c>(r: &'b mut R<'static>)
                                             -> &'b mut R<'c> {
    std::mem::transmute::<&'b mut R<'static>, &'b mut R<'c>>(r)
}
```

3.事实上我们还可以使用一些安全的方法来替代 transmute.

```plain
fn main() {
    /*Turning raw bytes(&[u8]) to u32, f64, etc.: */
    let raw_bytes = [0x78, 0x56, 0x34, 0x12];

    let num = unsafe { std::mem::transmute::<[u8; 4], u32>(raw_bytes) };

    // use `u32::from_ne_bytes` instead
    let num = u32::from_ne_bytes(raw_bytes);
    // or use `u32::from_le_bytes` or `u32::from_be_bytes` to specify the endianness
    let num = u32::from_le_bytes(raw_bytes);
    assert_eq!(num, 0x12345678);
    let num = u32::from_be_bytes(raw_bytes);
    assert_eq!(num, 0x78563412);

    /*Turning a pointer into a usize: */
    let ptr = &0;
    let ptr_num_transmute = unsafe { std::mem::transmute::<&i32, usize>(ptr) };

    // Use an `as` cast instead
    let ptr_num_cast = ptr as *const i32 as usize;

    /*Turning an &mut T into an &mut U: */
    let ptr = &mut 0;
    let val_transmuted = unsafe { std::mem::transmute::<&mut i32, &mut u32>(ptr) };

    // Now, put together `as` and reborrowing - note the chaining of `as`
    // `as` is not transitive
    let val_casts = unsafe { &mut *(ptr as *mut i32 as *mut u32) };

    /*Turning an &str into a &[u8]: */
    // this is not a good way to do this.
    let slice = unsafe { std::mem::transmute::<&str, &[u8]>("Rust") };
    assert_eq!(slice, &[82, 117, 115, 116]);

    // You could use `str::as_bytes`
    let slice = "Rust".as_bytes();
    assert_eq!(slice, &[82, 117, 115, 116]);

    // Or, just use a byte string, if you have control over the string
    // literal
    assert_eq!(b"Rust", &[82, 117, 115, 116]);
}
```

## 函数

Rust 代码中的函数和变量名使用 *snake case* 规范风格。在 snake case 中，所有字母都是小写并使用下划线分隔单词

我们在Rust 中通过输入 fn 后面跟着函数名和一对圆括号来定义函数。大括号告诉编译器哪里是函数体的开始和结尾。

### 参数

我们可以定义为拥有 **参数**（*parameters*）的函数，参数是特殊变量，是函数签名的一部分。当函数拥有参数（形参）时，可以为这些参数提供具体的值（实参）。技术上讲，这些具体值被称为参数（*arguments*）

```plain
fn main() {
    another_function(5);
}

fn another_function(x: i32) {
    println!("The value of x is: {x}");
}
```

当定义多个参数时，使用逗号分隔

### 语句与表达式

函数体由一系列的语句和一个可选的结尾表达式构成。

**语句**（*Statements*）是执行一些操作但不返回值的指令。

**表达式**（*Expressions*）计算并产生一个值。

语句不返回值，表达式会计算出一个值。

```plain
fn main() {
    let x = 5u32;

    let y = {
        let x_squared = x * x;
        let x_cube = x_squared * x;

        // 下面表达式的值将被赋给 `y`
        x_cube + x_squared + x
    };

    let z = {
        // 分号让表达式变成了语句，因此返回的不再是表达式 `2 * x` 的值，而是语句的值 `()`
        2 * x;
    };

    println!("x is {:?}", x);
    println!("y is {:?}", y);
    println!("z is {:?}", z);
}
```

语句 let y = 6; 中的 6 是一个表达式，它计算出的值是 6。**函数调用是一个表达式**。**宏调用是一个表达式**。**用大括号创建的一个新的块作用域也是一个表达式**，例如：

```plain
fn main() {
    let y = {
        let x = 3;
        x + 1
    };

    println!("The value of y is: {y}");
}
```

这个表达式：

```plain
{
    let x = 3;
    x + 1
}
```

是一个代码块，它的值是 4。表达式的结尾没有分号。如果在表达式的结尾加上分号，它就变成了语句，而语句不会返回值。

### 返回值

不对返回值命名，但要在箭头（->）后声明它的类型

在 Rust 中，函数的返回值等同于函数体最后一个表达式的值。使用 return 关键字和指定值，可从函数中提前返回；但大部分函数隐式的返回最后的表达式

```plain
fn main() {
    let x = plus_one(5);

    println!("The value of x is: {x}");
}

fn plus_one(x: i32) -> i32 {
    x + 1
}
```

#### **返回类型为()**

```plain
fn main(){
    println!("{}",type_of(&println!("helloworld")))
}
fn type_of<T>(_: &T) -> String{
    format!("{}",std::any::type_name::<T>())
}
//output:
//helloworld
//()
```

#### 返回类型为never

```plain
fn main() {
    never_return();
}

fn never_return() -> ! {
    // implement this function, don't modify fn signatures
    panic!("I return nothing!")
}
fn main() {
    never_return();
}

use std::thread;
use std::time;

fn never_return() -> ! {
    // implement this function, don't modify fn signatures
    loop {
        println!("I return nothing");
        // sleeping for 1 second to avoid exhausting the cpu resource
        thread::sleep(time::Duration::from_secs(1))
    }
}
```

#### 发散函数（Diverging function）

发散函数( Diverging function )不会返回任何值，因此它们可以用于替代需要返回任何值的地方

```plain
fn main() {
    println!("Success!");
}

fn get_option(tp: u8) -> Option<i32> {
    match tp {
        1 => {
            // TODO
        }
        _ => {
            // TODO
        }
    };
    
    // 这里与其返回一个 None，不如使用发散函数替代
    never_return_fn()
}

// 使用三种方法实现以下发散函数
fn never_return_fn() -> ! {
    
}
fn main() {
    println!("Success!");
}

fn get_option(tp: u8) -> Option<i32> {
    match tp {
        1 => {
            // TODO
        }
        _ => {
            // TODO
        }
    };

    never_return_fn()
}

// IMPLEMENT this function
// DON'T change any code else
fn never_return_fn() -> ! {
    unimplemented!()
}
// IMPLEMENT this function in THREE ways
fn never_return_fn() -> ! {
    panic!()
}

// IMPLEMENT this function in THREE ways
fn never_return_fn() -> ! {
    todo!();
}
// IMPLEMENT this function in THREE ways
fn never_return_fn() -> ! {
    loop {
        std::thread::sleep(std::time::Duration::from_secs(1))
    }
}
```

The difference between unimplemented! and [todo] is that while todo! conveys an intent of implementing the functionality later and the message is "not yet implemented", unimplemented! makes no such claims. Its message is "not implemented". Also some IDEs will mark todo!s.

**调用**

```plain
#[allow(unused)]

fn main() {
    get_option(3);
    println!("Success!");
}

fn get_option(tp: u8) -> Option<i32> {
    match tp {
        1 => {
            // TODO
        }
        _ => {
            // TODO
        }
    };

    never_return_fn()
}

// IMPLEMENT this function
// DON'T change any code else
fn never_return_fn() -> ! {
    loop {
        std::thread::sleep(std::time::Duration::from_secs(1))
    }
}
```

使用unimplemented!()和todo!();会报以下错误

thread 'main' panicked at 'not implemented', src\main.rs:24:5

```plain
let _v = match b {
        true => 1,
        // 发散函数也可以用于 `match` 表达式，用于替代任何类型的值
        false => {
            println!("Success!");
            panic!("we have no value for `false`, but we can panic")
        }
    };
```

## 控制流

### if表达式

```plain
fn main() {
    let number = 3;

    if number < 5 {
        println!("condition was true");
    } else {
        println!("condition was false");
    }
}
```

if 表达式中与条件关联的代码块有时被叫做 *arms

if/else 可以用作表达式来进行赋值

```plain
fn main() {
    let n = 5;

    let big_n =
        if n < 10 && n > -10 {
            println!(" 数字太小，先增加 10 倍再说");

            10 * n
        } else {
            println!("数字太大，我们得让它减半");

            n / 2 
        };

    println!("{} -> {}", n, big_n);
}
```

**注意**

1.代码中的条件 **必须** 是 bool 值。如果条件不是 bool 值，我们将得到一个错误。Rust 并不会尝试自动地将非布尔值转换为布尔值。必须总是显式地使用布尔值作为 if 的条件。

2.如果使用了多于1个else if最好使用match对代码进行重构

### 在let语句中使用if

因为 if 是一个表达式，我们可以在 let 语句的右侧使用它

```plain
fn main() {
    let condition = true;
    let number = if condition { 5 } else { 6 };

    println!("The value of number is: {number}");
}
```

if 的每个分支的可能的返回值都必须是相同类型

**注意**

if 代码块中的表达式返回一个整数，而 else 代码块中的表达式返回一个字符串。这不可行，因为变量必须只有一个类型。Rust 需要在编译时就确切的知道变量的类型

### 循环

#### loop

```plain
fn main() {
    loop {
        println!("again!");
    }
}
```

##### 从循环中返回值

```plain
fn main() {
    let mut counter = 0;

    let result = loop {
        counter += 1;

        if counter == 10 {
            break counter * 2;
        }
    };

    println!("The result is {result}");
}
```

##### 循环标签

如果存在嵌套循环，break 和 continue 应用于此时最内层的循环。你可以选择在一个循环上指定一个 **循环标签**（*loop label*），然后将标签与 break 或 continue 一起使用，使这些关键字应用于已标记的循环而不是最内层的循环

```plain
fn main() {
    let mut count = 0;
    'counting_up: loop {
        println!("count = {count}");
        let mut remaining = 10;

        loop {
            println!("remaining = {remaining}");
            if remaining == 9 {
                break;
            }
            if count == 2 {
                break 'counting_up;
            }
            remaining -= 1;
        }

        count += 1;
    }
    println!("End count = {count}");
}
```

### while

```plain
fn main() {
    let mut number = 3;

    while number != 0 {
        println!("{number}!");

        number -= 1;
    }

    println!("LIFTOFF!!!");
}
```

### for

```plain
fn main() {
    let a = [10, 20, 30, 40, 50];

    for element in a {
        println!("the value is: {element}");
    }
}
```

for循环遍历集合元素相较于while循环，增强了代码安全性，并消除了可能由于超出数组的结尾或遍历长度不够而缺少一些元素而导致的 bug

对于没有实现copy的可迭代对象for in 会取得所有权

```plain
fn main() {
    let names = [String::from("liming"),String::from("hanmeimei")];
    for name in &names {
        // do something with name...
    }

    println!("{:?}", names);

    let numbers = [1, 2, 3];
    // numbers中的元素实现了 Copy，因此无需转移所有权
    for n in numbers {
        // do something with name...
    }
    
    println!("{:?}", numbers);
}
```

#### 通过索引和值的方式迭代数组

```plain
fn main() {
    let a = [4,3,2,1];

    // 通过索引和值的方式迭代数组 `a` 
    for (i,v) in a.iter().enumerate() {
        println!("第{}个元素是{}",i+1,v);
    }
}
```

**Range**

它是标准库提供的类型，用来生成从一个数字开始到另一个数字之前结束的所有数字的序列。(不包括结束的数字)

rev方法可以反转range

```plain
fn main() {
    for number in (1..4).rev() {
        println!("{number}!");
    }
    println!("LIFTOFF!!!");
}
```

# 五.所有权

## 栈（Stack）与堆（Heap）

栈和堆都是代码在运行时可供使用的内存，但是它们的结构不同。栈以放入值的顺序存储值并以相反顺序取出值。这也被称作 **后进先出**（*last in, first out*）。

增加数据叫做 **进栈**（*pushing onto the stack*），而移出数据叫做 **出栈**（*popping off the stack*）。栈中的所有数据都必须占用已知且固定的大小。

在编译时大小未知或大小可能变化的数据，要改为存储在堆上。 堆是缺乏组织的：当向堆放入数据时，你要请求一定大小的空间。内存分配器（memory allocator）在堆的某处找到一块足够大的空位，把它标记为已使用，并返回一个表示该位置地址的 **指针**（*pointer*）。这个过程称作 **在堆上分配内存**（*allocating on the heap*），有时简称为 “分配”（allocating）。（将数据推入栈中并不被认为是分配）。因为指向放入堆中数据的指针是已知的并且大小是固定的，你可以将该指针存储在栈上，不过当需要实际数据时，必须访问指针。

入栈比在堆上分配内存要快，因为（入栈时）分配器无需为存储新数据去搜索内存空间；其位置总是在栈顶。相比之下，在堆上分配内存则需要更多的工作，这是因为分配器必须首先找到一块足够存放数据的内存空间，并接着做一些记录为下一次分配做准备。

访问堆上的数据比访问栈上的数据慢，因为必须通过指针来访问。现代处理器在内存中跳转越少就越快（缓存），出于同样原因，处理器在处理的数据彼此较近的时候（比如在栈上）比较远的时候（比如可能在堆上）能更好的工作。

当你的代码调用一个函数时，传递给函数的值（包括可能指向堆上数据的指针）和函数的局部变量被压入栈中。当函数结束时，这些值被移出栈。

跟踪哪部分代码正在使用堆上的哪些数据，最大限度的减少堆上的重复数据的数量，以及清理堆上不再使用的数据确保不会耗尽空间，这些问题正是所有权系统要处理的。一旦理解了所有权，你就不需要经常考虑栈和堆了，不过明白了所有权的主要目的就是为了管理堆数据，能够帮助解释为什么所有权要以这种方式工作。

## 所有权规则

1. **Rust 中的每一个值都有一个 所有者（*****owner*****）。**
2. **值在任一时刻有且只有一个所有者。**
3. **当所有者（变量）离开作用域，这个值将被丢弃。**

所有权的例子：

```plain
fn main() {
    let s = give_ownership();
    println!("{}", s);
}

// 只能修改下面的代码!
fn give_ownership() -> String {
    let s = String::from("hello, world");
    // convert String to Vec
    // 将 String 转换成 Vec 类型
    let _s = s.into_bytes();//into_bytes会转移所有权
    s
}
```

方法

```plain
fn main() {
    let s = give_ownership();
    println!("{}", s);
}

// Only modify the code below!
fn give_ownership() -> String {
    let s = String::from("hello, world");
    // convert String to Vec
    let _s = s.as_bytes();//as_bytes不会转移所有权
    s
}
```

或

```plain
fn main() {
    let s = give_ownership();
    println!("{}", s);
}

// Only modify the code below!
fn give_ownership() -> String {
    let s = String::from("hello, world");
    s
}
```

### 当所有权转移时，可变性也可以随之改变。

```plain
fn main() {
    let s = String::from("hello, ");
    
    let mut s1 = s;

    s1.push_str("world")
}
```

## 变量与作用域

作用域是一个项（item）在程序中有效的范围。假设有这样一个变量：

let s = "hello";

变量 s 绑定到了一个字符串字面值，这个字符串值是**硬编码**进程序代码中的。这个变量从声明的点开始直到当前 **作用域** 结束时都是有效的。示例 4-1 中的注释标明了变量 s 在何处是有效的。

```plain
{                      // s 在这里无效, 它尚未声明
        let s = "hello";   // 从此处起，s 是有效的

        // 使用 s
    }                      // 此作用域已结束，s 不再有效
```

- 当 s**进入作用域** 时，它就是有效的。
- 这一直持续到它 **离开作用域** 为止。

## str和&str

正常情况下我们无法使用 str 类型，但是可以使用 &str 来替代

```plain
fn main() {
    let s: &str = "hello, world";
}
```

如果要使用 str 类型，只能配合 Box。

```plain
fn main() {
    let s: Box<str> = "hello, world".into();
    greetings(s)
}

fn greetings(s: Box<str>) {
    println!("{}",s)
}
```

& 可以用来将 Box<str> 转换为 &str 类型

```plain
fn main() {
    let s: Box<str> = "hello, world".into();
    greetings(&s)
}

fn greetings(s: &str) {
    println!("{}",s)
}
```

## String类型

String 是定义在标准库中的类型，分配在堆上，可以动态的增长。它的底层存储是动态字节数组的方式( Vec<u8> )，但是与字节数组不同，String 是 UTF-8 编码。

Rust 有第二个字符串类型，String。这个类型管理被分配到堆上的数据，所以能够存储在编译时未知大小的文本。可以使用 from 函数基于字符串字面值来创建 String

let s = String::from("hello");

**可以** 修改此类字符串 ：

```plain
let mut s = String::from("hello");

    s.push_str(", world!"); // push_str() 在字符串后追加字面值

    println!("{}", s); // 将打印 `hello, world!`
```

## 内存与分配

就字符串字面值来说，我们在编译时就知道其内容，所以文本被直接硬编码进最终的可执行文件中。这使得字符串字面值快速且高效。不过这些特性都只得益于字符串字面值的不可变性。不幸的是，我们不能为了每一个在编译时大小未知的文本而将一块内存放入二进制文件中，并且它的大小还可能随着程序运行而改变。

对于 String 类型，为了支持一个可变，可增长的文本片段，需要在堆上分配一块在编译时未知大小的内存来存放内容。这意味着：

- 必须在运行时向内存分配器（memory allocator）请求内存。
- 需要一个当我们处理完 String 时将内存返回给分配器的方法。（某些语言的垃圾回收GC）

Rust 采取了一个不同的策略：**内存在拥有它的变量离开作用域后就被自动释放**。下面是示例 4-1 中作用域例子的一个使用 String 而不是字符串字面值的版本：

```plain
{
        let s = String::from("hello"); // 从此处起，s 是有效的

        // 使用 s
    }                                  // 此作用域已结束，
                                       // s 不再有效
```

这是一个将 String 需要的内存返回给分配器的很自然的位置：当 s 离开作用域的时候。当变量离开作用域，Rust 为我们调用一个特殊的函数。这个函数叫做 [drop](https://doc.rust-lang.org/std/ops/trait.Drop.html#tymethod.drop)，在这里 String 的作者可以放置释放内存的代码。Rust 在结尾的 } 处自动调用 drop。

## 变量与数据交互的方式

### 移动

**栈数据**

```plain
let x = 5;
 let y = x;
```

将 5 绑定到 x；接着生成一个值 x 的拷贝并绑定到 y”。现在有了两个变量，x 和 y，都等于 5

因为整数是有已知固定大小的简单值，所以这两个 5 被放入了栈中。

对于此类数据，移动和克隆没有区别

```plain
let s1 = String::from("hello");
let s2 = s1;
```

一个String由3部分组成：

1. 一个指向存放字符串内容的内存的指针
2. 一个长度len,指存放字符串内容所需的字节数
3. 一个容量capacity,指String从操作系统中总共获得内存的总字节数

上面这些**存放在栈上**

存放字符串内容的部分存放在heap上

当我们将 s1 赋值给 s2，String 的数据被复制了，这意味着我们从栈上拷贝了它的指针、长度和容量。我们并没有复制指针指向的堆上数据。

当变量离开时，会调用drop,导致double free

为了保证内存安全

- Rust没有尝试复制被分配的内存
- Rust让s1失效，即变量s1离开作用域时不需要释放任何东西（对应所有权规则2:值在任一时刻有且只有一个所有者）

```plain
let s1 = String::from("hello");
    let s2 = s1;

    println!("{}, world!", s1);
warning: unused variable: `s2`
 --> src\main.rs:3:9
  |
3 |     let s2 = s1;
  |         ^^ help: if this is intentional, prefix it with an underscore: `_s2`
  |
  = note: `#[warn(unused_variables)]` on by default

error[E0382]: borrow of moved value: `s1`
 --> src\main.rs:5:28
  |
2 |     let s1 = String::from("hello");
  |         -- move occurs because `s1` has type `String`, which does not implement the `Copy` trait
3 |     let s2 = s1;
  |              -- value moved here
4 |
5 |     println!("{}, world!", s1);
  |                            ^^ value borrowed here after move
  |
  = note: this error originates in the macro `$crate::format_args_nl` which comes from the expansion of the macro `println` (in Nightly builds, run with -Z macro-backtrace for more info)

For more information about this error, try `rustc --explain E0382`.
warning: `loop_test` (bin "loop_test") generated 1 warning
error: could not compile `loop_test` due to previous error; 1 warning emitted
```

rust的这种方式不同于浅拷贝，因为在浅拷贝的同时让被拷贝者失效了，因此使用新的术语：移动(Move)

**隐含的设计原则****rust不会自动创建数据的深拷贝**

因为就运行性能而言，任何自动赋值的操作都是廉价的

#### 部分move

当解构一个变量时，可以同时使用 move 和引用模式绑定的方式。当这么做时，部分 move 就会发生：变量中一部分的所有权被转移给其它变量，而另一部分我们获取了它的引用。

在这种情况下，原变量将无法再被使用，但是它没有转移所有权的那一部分依然可以使用，也就是之前被引用的那部分。

```plain
fn main() {
    #[derive(Debug)]
    struct Person {
        name: String,
        age: Box<u8>,
    }

    let person = Person {
        name: String::from("Alice"),
        age: Box::new(20),
    };

    // 通过这种解构式模式匹配，person.name 的所有权被转移给新的变量 `name`
    // 但是，这里 `age` 变量却是对 person.age 的引用, 这里 ref 的使用相当于: let age = &person.age 
    let Person { name, ref age } = person;

    println!("The person's age is {}", age);

    println!("The person's name is {}", name);

    // Error! 原因是 person 的一部分已经被转移了所有权，因此我们无法再使用它
    //println!("The person struct is {:?}", person);

    // 虽然 `person` 作为一个整体无法再被使用，但是 `person.age` 依然可以使用
    println!("The person's age from person struct is {}", person.age);
}
```

### 克隆

如果相对heap的数据进行深拷贝，而不仅仅时stack上面的数据，可以使用clone方法

```plain
let s1=String::from("Hello");
let s2=s1.clone();
println!("{},{}",s1,s2);
#[allow(unused)]

fn main() {
    let t = (String::from("hello"), String::from("world"));

    let (s1, s2) = t.clone();
 
    println!("{:?}, {:?}, {:?}", s1, s2, t); // -> "hello", "world", ("hello", "world")
}
```

### 复制

Copy trait,可以用于像整数这样完全放在stack上面的类型

- 如果一个类型实现了Copy trait，那么旧的变量在赋值后仍然可用
- 如果一个类型或该类型的一部分实现了Drop trait，那么Rust不允许让它再去实现Copy trait了

任何简单标量的组合类型都是Copy的

任何需要分配内存或某种资源的都不是Copy的

一些拥有Copy trait的类型：

- 所有整数类型，比如 u32。
- 布尔类型，bool，它的值是 true 和 false。
- 所有浮点数类型，比如 f64。
- 字符类型，char。
- 元组，当且仅当其包含的类型也都实现 Copy 的时候。比如，(i32, i32) 实现了 Copy，但 (i32, String) 就没有。

## 所有权与函数

将值传递给函数与给变量赋值的原理相似。向函数传递值可能会移动或者复制，就像赋值语句一样

```plain
fn main() {
    let s = String::from("hello");  // s 进入作用域

    takes_ownership(s);             // s 的值移动到函数里 ...
                                    // ... 所以到这里不再有效

    let x = 5;                      // x 进入作用域

    makes_copy(x);                  // x 应该移动函数里，
                                    // 但 i32 是 Copy 的，
                                    // 所以在后面可继续使用 x

} // 这里, x 先移出了作用域，然后是 s。但因为 s 的值已被移走，
  // 没有特殊之处

fn takes_ownership(some_string: String) { // some_string 进入作用域
    println!("{}", some_string);
} // 这里，some_string 移出作用域并调用 `drop` 方法。
  // 占用的内存被释放

fn makes_copy(some_integer: i32) { // some_integer 进入作用域
    println!("{}", some_integer);
} // 这里，some_integer 移出作用域。没有特殊之处
```

当尝试在调用 takes_ownership 后使用 s 时，Rust 会抛出一个编译时错误。

### 返回值与作用域

返回值也可以转移所有权

```plain
fn main() {
    let s1 = gives_ownership();         // gives_ownership 将返回值
                                        // 转移给 s1

    let s2 = String::from("hello");     // s2 进入作用域

    let s3 = takes_and_gives_back(s2);  // s2 被移动到
                                        // takes_and_gives_back 中,
                                        // 它也将返回值移给 s3
} // 这里, s3 移出作用域并被丢弃。s2 也移出作用域，但已被移走，
  // 所以什么也不会发生。s1 离开作用域并被丢弃

fn gives_ownership() -> String {             // gives_ownership 会将
                                             // 返回值移动给
                                             // 调用它的函数

    let some_string = String::from("yours"); // some_string 进入作用域.

    some_string                              // 返回 some_string 
                                             // 并移出给调用的函数
                                             // 
}

// takes_and_gives_back 将传入字符串并返回该值
fn takes_and_gives_back(a_string: String) -> String { // a_string 进入作用域
                                                      // 

    a_string  // 返回 a_string 并移出给调用的函数
}
```

变量的所有权总是遵循相同的模式：

- **将值赋给另一个变量时移动它**。
- 当持有堆中数据值的变量**离开作用域时**，其值将通过 drop 被清理掉，除非数据被移动为另一个变量所有。

如果让函数获得所得值而不获得所有权

```plain
fn main() {
    let s1 = String::from("hello");

    let (s2, len) = calculate_length(s1);

    println!("The length of '{}' is {}.", s2, len);
}

fn calculate_length(s: String) -> (String, usize) {
    let length = s.len(); // len() 返回字符串的长度

    (s, length)
}
```

这样过于麻烦，Rust 对此提供了一个不用获取所有权就可以使用值的功能，叫做 **引用**（*references*）。

## 引用与借用

```plain
fn main() {
    let s1 = String::from("hello");

    let len = calculate_length(&s1);

    println!("The length of '{}' is {}.", s1, len);
}

fn calculate_length(s: &String) -> usize {
    s.len()
}
```

**引用**（*reference*）像一个指针，因为**它是一个地址**，我们可以由此访问储存于该地址的属于其他变量的数据。 

与指针不同，**引用确保指向某个特定类型的有效值。**

```plain
fn main() {
   let x = 5;
   // 填写空白处
   let p = &x;

   println!("x 的内存地址是 {:p}", p); // output: 0x16fa3ac84
}
```

注意：与使用 & 引用相反的操作是 **解引用**（*dereferencing*），它使用解引用运算符，*

```plain
let s1 = String::from("hello");

    let len = calculate_length(&s1);
```

&s1 语法让我们创建一个 **指向** 值 s1 的引用，但是并不拥有它。因为并不拥有这个值，所以**当引用停止使用时，它所指向的值也不会被丢弃**。

我们将创建一个引用的行为称为 **借用**（*borrowing*）

正如变量默认是不可变的，引用也一样。**引用（默认）不允许修改引用的值。**

### rust会在某些情况下自动解引用

```plain
fn main() {
    let mut s = String::from("hello, ");

    let p = &mut s;
    
    p.push_str("world");
}
```

例子：

```plain
fn main() {
    let mut s = String::from("hello, ");

    borrow_object(&s)
}

fn borrow_object(s: &String) {}
fn main() {
    let mut s = String::from("hello, ");

    push_str(&mut s)
}

fn push_str(s: &mut String) {
    s.push_str("world")
}
```

### 可变引用

```plain
fn main() {
    let mut s = String::from("hello");

    change(&mut s);
}

fn change(some_string: &mut String) {
    some_string.push_str(", world");
}
```

可变引用有一个很大的限制：**如果你有一个对该变量的可变引用，你就不能再创建对该变量的引用。这些尝试创建两个** **s** **的可变引用的代码会失败**(这**没有考虑NIL**)

```plain
let mut s = String::from("hello");

    let r1 = &mut s;
    let r2 = &mut s;

    println!("{}, {}", r1, r2);
```

考虑NIL这段代码不会报错

```plain
let mut s = String::from("hello");

    let r1 = &mut s;
    let r2 = &mut s;

    println!("{}, {}", r1, r2);
```

这个限制的好处是 Rust 可以在编译时就避免数据竞争。**数据竞争**（*data race*）类似于竞态条件，它可由这三个行为造成：

- 两个或更多指针同时访问同一数据。
- 至少有一个指针被用来写入数据。
- 没有同步数据访问的机制。

可以使用大括号来创建一个新的作用域，以允许拥有多个可变引用，只是不能 **同时** 拥有：

```plain
let mut s = String::from("hello");

    {
        let r1 = &mut s;
    } // r1 在这里离开了作用域，所以我们完全可以创建一个新的引用

    let r2 = &mut s;
```

另外一个限制：**不可以同时拥有一个可变引用和一个不可变的引用**

但是**多个不可变的引用是可以的**

```plain
let mut s = String::from("hello");

    let r1 = &s; // 没问题
    let r2 = &s; // 没问题
    let r3 = &mut s; // 大问题

    println!("{}, {}, and {}", r1, r2, r3);
```

### 悬空引用（悬垂引用Dangling References)

在 Rust 中编译器确保引用永远也不会变成悬垂状态：当你拥有一些数据的引用，编译器确保数据不会在其引用之前离开作用域。

让我们尝试创建一个悬垂引用，Rust 会通过一个编译时错误来避免：

文件名: src/main.rs

```plain
fn main() {
    let reference_to_nothing = dangle();
}

fn dangle() -> &String {
    let s = String::from("hello");//s在函数结束后就drop了
    &s//返回引用，但是函数结束后该地址就被释放掉了
}
Compiling loop_test v0.1.0 (C:\Users\cauchy\Desktop\rust\loop_test)
error[E0106]: missing lifetime specifier
 --> src\main.rs:5:16
  |
5 | fn dangle() -> &String {
  |                ^ expected named lifetime parameter
  |
  = help: this function's return type contains a borrowed value, but there is no value for it to be borrowed from
help: consider using the `'static` lifetime
  |
5 | fn dangle() -> &'static String {
  |                 +++++++

For more information about this error, try `rustc --explain E0106`.
error: could not compile `loop_test` due to previous error
```

### ref

ref 与 & 类似，可以用来获取一个值的引用，但是它们的用法有所不同。

```plain
fn main() {
    let c = '中';

    let r1 = &c;
    // 填写空白处，但是不要修改其它行的代码
    let ref r2 = c;

    assert_eq!(*r1, *r2);
    
    // 判断两个内存地址的字符串是否相等
    assert_eq!(get_addr(r1),get_addr(r2));
}

// 获取传入引用的内存地址的字符串形式
fn get_addr(r: &char) -> String {
    format!("{:p}", r)
}
```

### 引用规则(借用规则)总结

- 在任意给定时间，**要么** 只能有一个可变引用，**要么** 只能有多个不可变引用。
- 引用必须总是有效的。

Ok: 从可变对象借用不可变

```plain
fn main() {
    let mut s = String::from("hello, ");

    borrow_object(&s);
    
    s.push_str("world");
}

fn borrow_object(s: &String) {}
```

### None Lexical Lifetimes(NLL)非词法作用域生命周期

https://zhuanlan.zhihu.com/p/32884290

例子

```plain
// 注释掉一行代码让它工作
fn main() {
    let mut s = String::from("hello, ");

    let r1 = &mut s;
    r1.push_str("world");
    let r2 = &mut s;
    r2.push_str("!");
    
    println!("{}",r1);
}
```

注释掉println即可

```plain
fn main() {
    let mut s = String::from("hello, ");

    let r1 = &mut s;
    r1.push_str("world");//rust编译器知道这之后r1对s的借用生命周期结束了
    let r2 = &mut s;
    r2.push_str("!");
    
    //println!("{}",r1);
}
fn main() {
    let mut x = 22;

    let p = &mut x; // mutable borrow

    println!("{}", x); // later used
}
```

这段代码顺利编译，因为编译器知道 x 的可变借用并没有持续到作用域结尾，而是在 x 被再次使用之前就结束了，所以这里不存在冲突。

```plain
fn main() {
    let mut s = String::from("hello, ");

    let r1 = &mut s;
    let r2 = &mut s;

    // 在下面增加一行代码人为制造编译错误：cannot borrow `s` as mutable more than once at a time
    // 你不能同时使用 r1 和 r2
     
}
```

加入r1.push_str("world");即可

## 切片 Slice 类型

*slice* 允许你引用集合中一段连续的元素序列，而不用引用整个集合。slice 是一类引用，所以它没有所有权。

```plain
fn first_word(s: &String) -> usize {
    let bytes = s.as_bytes();

    for (i, &item) in bytes.iter().enumerate() {
        if item == b' ' {
            return i;
        }
    }
    s.len()
}
```

该函数接收一个用空格分隔单词的字符串，并返回在该字符串中找到的第一个单词。如果函数在该字符串中并未找到空格，则整个字符串就是一个单词，所以应该返回整个字符串。

first_word 函数有一个参数 &String。因为我们不需要所有权，所以这没有问题。不过应该返回什么呢？我们并没有一个真正获取 **部分** 字符串的办法。不过，我们可以返回单词结尾的索引，结尾由一个空格表示

因为需要逐个元素的检查 String 中的值是否为空格，需要用 as_bytes 方法将 String 转化为字节数组：

​    let bytes = s.as_bytes();

接下来，使用 iter 方法在字节数组上创建一个迭代器：

​    for (i, &item) in bytes.iter().enumerate() {

因为 enumerate 方法返回一个元组，我们可以使用模式来解构，所以在 for 循环中，我们指定了一个模式，其中元组中的 i 是索引而元组中的 &item 是单个字节。因为我们从 .iter().enumerate() 中获取了集合元素的引用，所以模式中使用了 &。

不过这有一个问题。我们返回了一个独立的 usize，不过它只在 &String 的上下文中才是一个有意义的数字。换句话说，因为它是一个与 String 相分离的值，无法保证将来它仍然有效。

### 字符串切片string slice

**字符串 slice**（*string slice*）是 String 中一部分值的引用，它看起来像这样：

```plain
let s = String::from("hello world");

    let hello = &s[0..5];
    let world = &s[6..11];
```

**[开始索引..终止索引]**

**[starting_index..ending_index]**

其中 starting_index 是 slice 的第一个位置，ending_index 则是 slice **最后一个位置的后一个值。**

如果想要从索引 0 开始，可以不写两个点号之前的值

```plain
let s = String::from("hello");

let slice = &s[0..2];
let slice = &s[..2];
```

如果 slice 包含 String 的最后一个字节，也可以舍弃尾部的数字

```plain
let s = String::from("hello");

let len = s.len();

let slice = &s[3..len];
let slice = &s[3..];
```

也可以同时舍弃这两个值来获取整个字符串的 slice

```plain
let s = String::from("hello");

let len = s.len();

let slice = &s[0..len];
let slice = &s[..];
```

**注意**：字符串 slice range 的索引必须位于有效的 UTF-8 字符边界内，如果尝试从一个多字节字符的中间位置创建字符串 slice，则程序将会因错误而退出。

```plain
fn main() {
    let s = "你好，世界";
    let slice = &s[0..3];
    println!("{}",slice);
    assert!(slice == "你");
}
```

重写函数，返回一个slice(字符串切片返回值可以写成：&str)

```plain
fn first_word(s: &String) -> &str {
    let bytes = s.as_bytes();

    for (i, &item) in bytes.iter().enumerate() {
        if item == b' ' {
            return &s[0..i];
        }
    }

    &s[..]
}
```

调用

```plain
fn main() {
    let mut s = String::from("hello world");

    let word = first_word(&s);

    s.clear(); // 错误!

    println!("the first word is: {}", word);
}
```

当拥有某值的不可变引用时，就不能再获取一个可变引用。因为 clear 需要清空 String，它尝试获取一个可变引用。在调用 clear 之后的 println! 使用了 word 中的引用，所以这个不可变的引用在此时必须仍然有效。Rust 不允许 clear 中的可变引用和 word 中的不可变引用同时存在，因此编译失败

### 字符串字面值就是slice

let s = "Hello, world!";

这里 s 的类型是 &str：它是一个指向二进制程序特定位置的 slice,这也就是为什么字符串字面值是不可变的；&str 是一个不可变引用。

### 字符串slice作为参数

在知道了能够获取字面值和 String 的 slice 后，我们对 first_word 做了改进，这是它的签名：

fn first_word(s: &String) -> &str {

而更有经验的 Rustacean 会编写出如下的签名，因为它使得可以对 &String 值和 &str 值使用相同的函数：

fn first_word(s: &str) -> &str {

如果有一个字符串 slice，可以直接传递它。如果有一个 String，则可以传递整个 String 的 slice 或对 String 的引用。这种灵活性利用了 *deref coercions* 的优势，定义一个获取字符串 slice 而不是 String 引用的函数使得我们的 API 更加通用并且不会丢失任何功能：

```plain
fn main() {
    let my_string = String::from("hello world");

    // `first_word` 适用于 `String`（的 slice），整体或全部
    let word = first_word(&my_string[0..6]);
    let word = first_word(&my_string[..]);
    // `first_word` 也适用于 `String` 的引用，
    // 这等价于整个 `String` 的 slice
    let word = first_word(&my_string);

    let my_string_literal = "hello world";

    // `first_word` 适用于字符串字面值，整体或全部
    let word = first_word(&my_string_literal[0..6]);
    let word = first_word(&my_string_literal[..]);

    // 因为字符串字面值已经 **是** 字符串 slice 了，
    // 这也是适用的，无需 slice 语法！
    let word = first_word(my_string_literal);
}
```

**&String可以被隐式地转换为&str类型**

```plain
fn main() {
    let mut s = String::from("hello world");

    // 这里, &s 是 `&String` 类型，但是 `first_character` 函数需要的是 `&str` 类型。
    // 尽管两个类型不一样，但是代码仍然可以工作，原因是 `&String` 会被隐式地转换成 `&str` 类型，如果大家想要知道更多，可以看看 Deref 章节: https://course.rs/advance/smart-pointer/deref.html
    let ch = first_character(&s);

    println!("the first character is: {}", ch);
    s.clear();
}
fn first_character(s: &str) -> &str {
    &s[..1]
}
```

### 其他类型的slice

字符串 slice，是针对字符串的。不过也有更通用的 slice 类型。考虑一下这个数组：

let a = [1, 2, 3, 4, 5];

就跟我们想要获取字符串的一部分那样，我们也会想要引用数组的一部分。我们可以这样做：

```plain
let a = [1, 2, 3, 4, 5];

let slice = &a[1..3];

assert_eq!(slice, &[2, 3]);
```

这个 slice 的类型是 &[i32]。它跟字符串 slice 的工作方式一样，通过存储第一个集合元素的引用和一个集合总长度。你可以对其他所有集合使用这类 slice。

切片跟数组相似，但是**切片的长度无法在编译期得知**，因此你**无法直接使用切片类型**。

```plain
// 修复代码中的错误，不要新增代码行!
fn main() {
    let arr = [1, 2, 3];
    let s1: [i32] = arr[0..2];

    let s2: str = "hello, world" as str;
}
//修复后
fn main() {
    let arr = [1, 2, 3];
    let s1: &[i32] = &arr[0..2];

    let s2: &str = "hello, world" as &str;
}
```

- 一个切片引用占用了**2个字**大小的内存空间( 从现在开始，为了简洁性考虑，如无特殊原因，我们统一使用切片来特指切片引用 )。 该切片的第一个字是指向数据的指针，第二个字是切片的长度。
- 字的大小取决于处理器架构，例如在 x86-64 上，字的大小是 64 位也就是 8 个字节，那么一个切片引用就是 16 个字节大小。

```plain
fn main() {
    let arr: [char; 3] = ['中', '国', '人'];

    let slice = &arr[..2];
    
    assert!(std::mem::size_of_val(&slice) == 16);
}
```

- 切片( 引用 )可以用来**借用数组的某个连续的部分**，对应的签名是 &[T]，可以与数组的签名对比下 [T; Length]。

```plain
fn main() {
   let arr: [i32; 5] = [1, 2, 3, 4, 5];
  
  let slice: &[i32] = &arr[1..4];
  assert_eq!(slice, &[2, 3, 4]);
}
```

# 六.结构体

## 定义结构体

需要使用 struct 关键字并为整个结构体提供一个名字。

在大括号中，定义每一部分数据的名字和类型，我们称为 **字段**（*field*）

```plain
struct User {
    active: bool,
    username: String,
    email: String,
    sign_in_count: u64,
}
```

## 实例化

```plain
fn main() {
    let user1 = User {
        email: String::from("someone@example.com"),
        username: String::from("someusername123"),
        active: true,
        sign_in_count: 1,
    };
}
```

你可以在实例化一个结构体时将它整体标记为可变的，但是 Rust **不允许我们将结构体的某个字段专门指定为可变的.**

```plain
struct Person {
    name: String,
    age: u8,
}
fn main() {
    let age = 18;
    let mut p = Person {
        name: String::from("sunface"),
        age,
    };
    p.age = 30;
    p.name = String::from("sunfei");
}
```

## 访问

```plain
fn main() {
    let mut user1 = User {
        email: String::from("someone@example.com"),
        username: String::from("someusername123"),
        active: true,
        sign_in_count: 1,
    };

    user1.email = String::from("anotheremail@example.com");
}
```

一旦struct的实例是可变的，那么实例中所有的字段都是可变的

## 字段初始化简写

当字段名与字段值对应的变量名相同时，就可以使用字段初始化简写的方式

```plain
fn build_user(email: String, username: String) -> User {
    User {
        email,
        username,
        active: true,
        sign_in_count: 1,
    }
}
```

## Struct更新语法

基于 现有的struct实例创建一个新的实例

```plain
fn main() {
    // --snip--

    let user2 = User {
        active: user1.active,
        username: user1.username,
        email: String::from("another@example.com"),
        sign_in_count: user1.sign_in_count,
    };
}
```

使用struct更新语法

```plain
fn main() {
    // --snip--

    let user2 = User {
        email: String::from("another@example.com"),
        ..user1
    };
}
```

## Tuple Struct元组结构体

元组结构体有着结构体名称提供的含义，但没有具体的字段名，只有字段的类型。

适用于给整个元组取一个名字，并使元组成为与其他元组不同的类型时

```plain
struct Color(i32, i32, i32);
struct Point(i32, i32, i32);

fn main() {
    let black = Color(0, 0, 0);
    let origin = Point(0, 0, 0);
}
```

## 没有任何字段的类单元结构体（unit-like structs）

**没有任何字段的类单元结构体**,它们类似于 ()

```plain
struct AlwaysEqual;

fn main() {
    let subject = AlwaysEqual;
}
```

## struct中的所有权

在示例 5-1 中的 User 结构体的定义中，我们使用了自身拥有所有权的 String 类型而不是 &str 字符串 slice 类型。这是一个有意而为之的选择，因为我们想要**这个结构体拥有它所有的数据**，**为此只要整个结构体是有效的话其数据也是有效的。**

可以使结构体存储被其他对象拥有的数据的引用，不过这么做的话需要用上 **生命周期**（*lifetimes*）

```plain
struct User {
    active: bool,
    username: &str,
    email: &str,
    sign_in_count: u64,
}

fn main() {
    let user1 = User {
        email: "someone@example.com",
        username: "someusername123",
        active: true,
        sign_in_count: 1,
    };
}
```

报错：缺少生命周期标识符

## 打印struct

```plain
#[derive(Debug)]
struct Rectangle{
    width: u32,
    length: u32,
}
fn main() {
    let rect=Rectangle{
        width:30,
        length:50,
    };
    println!("{}",area(&rect));
    println!("{:#?}",rect)
}
fn area(rect: &Rectangle)->u32{
    rect.width*rect.length
}
```

## struct 的方法

```plain
#[derive(Debug)]
struct Rectangle{
    width: u32,
    length: u32,
}
impl Rectangle{
    fn area(&self)->u32{
        self.width*self.length
    }
}
fn main() {
    let rect=Rectangle{
        width:30,
        length:50,
    };
    println!("{}",rect.area());
    println!("{:#?}",rect)
}
```

1. 在impl块里面定义方法
2. 方法的第一个参数可以是&self，也可以获得其所有权或可变借用，和其他参数一样
3. 更良好的代码组织

## 方法调用的运算符

在 C/C++ 语言中，有两个不同的运算符来调用方法：. 直接在对象上调用方法，而 -> 在一个对象的指针上调用方法，这时需要先解引用（dereference）指针。换句话说，如果 object 是一个指针，那么 object->something() 就像 (*object).something() 一样。

Rust 并没有一个与 -> 等效的运算符；相反，Rust 有一个叫 **自动引用和解引用**（*automatic referencing and dereferencing*）的功能。**方法调用**是 Rust 中**少数几个拥有这种行为的地方**。

它是这样工作的：当使用 object.something() 调用方法时，Rust 会自动为 object 添加 &、&mut 或 * 以便使 object 与方法签名匹配。也就是说，这些代码是等价的：

```plain
p1.distance(&p2);
(&p1).distance(&p2);
```

这种自动引用的行为之所以有效，是因为方法有一个明确的接收者———— self 的类型。在给出接收者和方法名的前提下，Rust 可以明确地计算出方法是仅仅读取（&self），做出修改（&mut self）或者是获取所有权（self）。

## 关联函数

```plain
impl Rectangle {
    fn square(size: u32) -> Self {
        Self {
            width: size,
            height: size,
        }
    }
}
```

所有在 impl 块中定义的函数被称为 **关联函数**（*associated functions*）

不是方法的关联函数经常被用作返回一个结构体新实例的构造函数。这些函数的名称通常为 new ，但 new 并不是一个关键字。

使用结构体名和 :: 语法来调用这个关联函数：比如 let sq = Rectangle::square(3);。这个函数位于结构体的命名空间中：:: 语法用于关联函数和模块创建的命名空间

每个结构体都允许拥有多个 impl 块。

## 示例

```plain
struct Point {
    x: f64,
    y: f64,
}

// `Point` 的关联函数都放在下面的 `impl` 语句块中
impl Point {
    // 关联函数的使用方法跟构造器非常类似
    fn origin() -> Point {
        Point { x: 0.0, y: 0.0 }
    }

    // 另外一个关联函数，有两个参数
    fn new(x: f64, y: f64) -> Point {
        Point { x: x, y: y }
    }
}

struct Rectangle {
    p1: Point,
    p2: Point,
}

impl Rectangle {
    // 这是一个方法
    // `&self` 是 `self: &Self` 的语法糖
    // `Self` 是当前调用对象的类型，对于本例来说 `Self` = `Rectangle`
    fn area(&self) -> f64 {
        // 使用点操作符可以访问 `self` 中的结构体字段
        let Point { x: x1, y: y1 } = self.p1;
        let Point { x: x2, y: y2 } = self.p2;

  
        // `abs` 是一个 `f64` 类型的方法，会返回调用者的绝对值
        ((x1 - x2) * (y1 - y2)).abs()
    }

    fn perimeter(&self) -> f64 {
        let Point { x: x1, y: y1 } = self.p1;
        let Point { x: x2, y: y2 } = self.p2;

        2.0 * ((x1 - x2).abs() + (y1 - y2).abs())
    }

    // 该方法要求调用者是可变的，`&mut self` 是 `self: &mut Self` 的语法糖
    fn translate(&mut self, x: f64, y: f64) {
        self.p1.x += x;
        self.p2.x += x;

        self.p1.y += y;
        self.p2.y += y;
    }
}

// `Pair` 持有两个分配在堆上的整数
struct Pair(Box<i32>, Box<i32>);

impl Pair {
    // 该方法会拿走调用者的所有权
    // `self` 是 `self: Self` 的语法糖
    fn destroy(self) {
        let Pair(first, second) = self;

        println!("Destroying Pair({}, {})", first, second);

        // `first` 和 `second` 在这里超出作用域并被释放
    }
}

fn main() {
    let rectangle = Rectangle {
        // 关联函数的调用不是通过点操作符，而是使用 `::`
        p1: Point::origin(),
        p2: Point::new(3.0, 4.0),
    };

    // 方法才是通过点操作符调用
    // 注意，这里的方法需要的是 `&self` 但是我们并没有使用 `(&rectangle).perimeter()` 来调用，原因在于：
    // 编译器会帮我们自动取引用
    //  `rectangle.perimeter()` === `Rectangle::perimeter(&rectangle)`
    println!("Rectangle perimeter: {}", rectangle.perimeter());
    println!("Rectangle area: {}", rectangle.area());

    let mut square = Rectangle {
        p1: Point::origin(),
        p2: Point::new(1.0, 1.0),
    };


    // 错误！`rectangle` 是不可变的，但是这个方法要求一个可变的对象
    //rectangle.translate(1.0, 0.0);
    // TODO ^ 试着反注释此行，看看会发生什么

    // 可以！可变对象可以调用可变的方法
    square.translate(1.0, 1.0);

    let pair = Pair(Box::new(1), Box::new(2));

    pair.destroy();

    // Error! 上一个 `destroy` 调用拿走了 `pair` 的所有权
    //pair.destroy();
    // TODO ^ 试着反注释此行
}
```

# 七.枚举

## 定义枚举

```plain
enum IpAddrKind {
    V4,
    V6,
}
```

通过在代码中定义一个 IpAddrKind 枚举来表现这个概念并列出可能的 IP 地址类型，V4 和 V6。这被称为枚举的 **成员**（*variants*）：

在创建枚举时，你可以使用显式的**整数**设定枚举成员的值。

```plain
enum Number {
    Zero,
    One,
    Two,
}

enum Number1 {
    Zero = 0,
    One,
    Two,
}

// 错误，不能用小数
//enum Number2 {
//    Zero = 0.0,
//    One = 1.0,
//    Two = 2.0,
//}

// C-like enum
enum Number2 {
    Zero = 0,
    One = 1,
    Two = 2,
}

fn main() {
    // 通过 `as` 可以将枚举值强转为整数类型
    assert_eq!(Number::One as u8, Number1::One as u8);
    assert_eq!(Number1::One as u8, Number2::One as u8);
}
```

## 枚举值

可以像这样创建 IpAddrKind 两个不同成员的实例：

```plain
let four = IpAddrKind::V4;
    let six = IpAddrKind::V6;
```

枚举成员中的值可以使用模式匹配来获取

```plain
enum Message {
    Quit,
    Move { x: i32, y: i32 },
    Write(String),
    ChangeColor(i32, i32, i32),
}

fn main() {
    let msg = Message::Move{x: 1, y: 1};

    if let Message::Move{x:a,y: b} = msg {
        assert_eq!(a, b);
    } else {
        panic!("不要让这行代码运行！");
    }
}
```

## 将数据附加到枚举的变体中

使用struct

```plain
enum IpAddrKind {
        V4,
        V6,
    }

    struct IpAddr {
        kind: IpAddrKind,
        address: String,
    }

    let home = IpAddr {
        kind: IpAddrKind::V4,
        address: String::from("127.0.0.1"),
    };

    let loopback = IpAddr {
        kind: IpAddrKind::V6,
        address: String::from("::1"),
    };
```

仅仅使用枚举并将数据直接放进每一个枚举成员而不是将枚举作为结构体的一部分。IpAddr 枚举的新定义表明了 V4 和 V6 成员都关联了 String 值：

```plain
enum IpAddr {
        V4(String),
        V6(String),
    }

    let home = IpAddr::V4(String::from("127.0.0.1"));

    let loopback = IpAddr::V6(String::from("::1"));
```

我们直接将数据附加到枚举的每个成员上，这样就不需要一个额外的结构体了。

```plain
enum IpAddr {
        V4(u8, u8, u8, u8),
        V6(String),
    }

    let home = IpAddr::V4(127, 0, 0, 1);

    let loopback = IpAddr::V6(String::from("::1"));
```

注意虽然标准库中包含一个 IpAddr 的定义，仍然可以创建和使用我们自己的定义而不会有冲突，因为我们并没有将标准库中的定义引入作用域。

枚举可以嵌入多种类型

```plain
enum Message {
    Quit,
    Move { x: i32, y: i32 },
    Write(String),
    ChangeColor(i32, i32, i32),
}
```

- Quit 没有关联任何数据。
- Move 类似结构体包含命名字段。
- Write 包含单独一个 String。
- ChangeColor 包含三个 i32。

## 枚举中定义函数

结构体和枚举还有另一个相似点：就像可以使用 impl 来为结构体定义方法那样，也可以在枚举上定义方法。这是一个定义于我们 Message 枚举上的叫做 call 的方法：

```plain
impl Message {
        fn call(&self) {
            // 在这里定义方法体
        }
    }

    let m = Message::Write(String::from("hello"));
    m.call();
```

方法体使用了 self 来获取调用方法的值。这个例子中，创建了一个值为 Message::Write(String::from("hello")) 的变量 m，而且这就是当 m.call() 运行时 call 方法中的 self 的值。

```plain
#[derive(Debug)]
enum TrafficLightColor {
    Red,
    Yellow,
    Green,
}

// implement TrafficLightColor with a method
impl TrafficLightColor {
    fn color(&self) -> String {
        match *self {
            TrafficLightColor::Red => "red".to_string(),
            TrafficLightColor::Yellow => "yellow".to_string(),
            TrafficLightColor::Green => "green".to_string(),
        }
    }
}

fn main() {
    let c = TrafficLightColor::Yellow;

    assert_eq!(c.color(), "yellow");

    println!("{:?}", c);
}
```

## Option枚举

定义域标准库中，在prelude(预导入模块中)

**Rust没有Null**，提供了类似于Null概念的枚举-Option<T>,它定义于标准库中

```plain
enum Option<T> {
    None,
    Some(T),
}
```

使用

```plain
let some_number = Some(5);
    let some_char = Some('e');

    let absent_number: Option<i32> = None;
```

当有一个 Some 值时，我们就知道存在一个值，而这个值保存在 Some 中。当有个 None 值时，在某种意义上，它跟空值具有相同的意义：并没有一个有效的值。那么，Option<T> 为什么就比空值要好呢？

简而言之，因为 Option<T> 和 T（这里 T 可以是任何类型）是不同的类型，编译器不允许像一个肯定有效的值那样使用 Option<T>。例如，这段代码不能编译，因为它尝试将 Option<i8> 与 i8 相加：

```plain
let x: i8 = 5;
    let y: Option<i8> = Some(5);

    let sum = x + y;
```

事实上，错误信息意味着 Rust 不知道该如何将 Option<i8> 与 i8 相加，因为它们的类型不同。当在 Rust 中拥有一个像 i8 这样类型的值时，编译器确保它总是有一个有效的值。我们可以自信使用而无需做空值检查。只有当使用 Option<i8>（或者任何用到的类型）的时候需要担心可能没有值，而编译器会确保我们在使用值之前处理了为空的情况。

换句话说，在对 Option<T> 进行 T 的运算之前必须将其转换为 T。

为了拥有一个可能为空的值，你必须要显式的将其放入对应类型的 Option<T> 中。接着，当使用这个值时，必须明确的处理值为空的情况。只要一个值不是 Option<T> 类型，你就 **可以** 安全的认定它的值不为空。

这是 Rust 的一个经过深思熟虑的设计决策，来限制空值的泛滥以增加 Rust 代码的安全性。

## 枚举实现链表

```plain
#[allow(unused)]
// 填空，让代码运行
use crate::List::*;

enum List {
    // Cons: 链表中包含有值的节点，节点是元组类型，第一个元素是节点的值，第二个元素是指向下一个节点的指针
    Cons(u32, Box<List>),
    // Nil: 链表中的最后一个节点，用于说明链表的结束
    Nil,
}

// 为枚举实现一些方法
impl List {
    // 创建空的链表
    fn new() -> List {
        // 因为没有节点，所以直接返回 Nil 节点
        // 枚举成员 Nil 的类型是 List
        Nil
    }

    // 在老的链表前面新增一个节点，并返回新的链表
    fn prepend(self, elem: u32) -> List {
        Cons(elem, Box::new(self))
    }

    // 返回链表的长度
    fn len(&self) -> u32 {
        match *self {
            // 这里我们不能拿走 tail 的所有权，因此需要获取它的引用，递归计算
            Cons(_,ref tail) => 1 + tail.len(),
            // 空链表的长度为 0
            Nil => 0
        }
    }

    // 返回链表的字符串表现形式，用于打印输出
    fn stringify(&self) -> String {
        match *self {
            Cons(head, ref tail) => {
                // 递归生成字符串
                format!("{}, {}", head, tail.stringify())
            },
            Nil => {
                format!("Nil")
            },
        }
    }
}

fn main() {
    // 创建一个新的链表(也是空的)
    let mut list = List::new();

    // 添加一些元素
    list = list.prepend(1);
    list = list.prepend(2);
    list = list.prepend(3);

    // 打印列表的当前状态
    println!("链表的长度是: {}", list.len());
    println!("{}", list.stringify());
}
```

# 八.模式匹配

## match控制流结构

Rust 有一个叫做 match 的极为强大的控制流运算符，它允许我们将**一个值**与**一系列的模式**相比较，并根据相匹配的模式执行相应代码

```plain
enum Coin {
    Penny,
    Nickel,
    Dime,
    Quarter,
}

fn value_in_cents(coin: Coin) -> u8 {
    match coin {
        Coin::Penny => 1,
        Coin::Nickel => 5,
        Coin::Dime => 10,
        Coin::Quarter => 25,
    }
}
fn value_in_cents(coin: Coin) -> u8 {
    match coin {
        Coin::Penny => {
            println!("Lucky penny!");
            1
        }
        Coin::Nickel => 5,
        Coin::Dime => 10,
        Coin::Quarter => 25,
    }
}
```

### matches!

matches!看起来像 match, 但是它可以做一些特别的事情

```plain
fn main() {
    let alphabets = ['a', 'E', 'Z', '0', 'x', '9' , 'Y'];

    // fill the blank with `matches!` to make the code work
    for ab in alphabets {
        assert!(matches!(ab, 'a'..='z' | 'A'..='Z' | '0'..='9'))
    }
}
```

下面的代码会报错

```plain
enum MyEnum {
    Foo,
    Bar
}

fn main() {
    let mut count = 0;

    let v = vec![MyEnum::Foo,MyEnum::Bar,MyEnum::Foo];
    for e in v {
        if e == MyEnum::Foo { // 修复错误，只能修改本行代码
            count += 1;
        }
    }

    assert_eq!(count, 2);
}
Compiling demo v0.1.0 (C:\Users\cauchy\Desktop\rust\demo)
error[E0369]: binary operation `==` cannot be applied to type `MyEnum`
  --> src\main.rs:13:14
   |
13 |         if e == MyEnum::Foo { // 修复错误，只能修改本行代码
   |            - ^^ ----------- MyEnum
   |            |
   |            MyEnum
   |
note: an implementation of `PartialEq<_>` might be missing for `MyEnum`
  --> src\main.rs:3:1
   |
3  | enum MyEnum {
   | ^^^^^^^^^^^ must implement `PartialEq<_>`
help: consider annotating `MyEnum` with `#[derive(PartialEq)]`
   |
3  | #[derive(PartialEq)]
   |

For more information about this error, try `rustc --explain E0369`.
error: could not compile `demo` due to previous error
```

修改为

```plain
enum MyEnum {
    Foo,
    Bar
}

fn main() {
    let mut count = 0;

    let v = vec![MyEnum::Foo,MyEnum::Bar,MyEnum::Foo];
    for e in v {
        if matches!(e, MyEnum::Foo) { // 修复错误，只能修改本行代码
            count += 1;
        }
    }

    assert_eq!(count, 2);
}
```

### 绑定值的模式

匹配分支的另一个有用的功能是可以绑定匹配的模式的部分值。这也就是如何从枚举成员中提取值的。

```plain
#[derive(Debug)] // 这样可以立刻看到州的名称
enum UsState {
    Alabama,
    Alaska,
    // --snip--
}

enum Coin {
    Penny,
    Nickel,
    Dime,
    Quarter(UsState),
}

fn value_in_cents(coin: Coin) -> u8 {
    match coin {
        Coin::Penny => 1,
        Coin::Nickel => 5,
        Coin::Dime => 10,
        Coin::Quarter(state) => {
            println!("State quarter from {:?}!", state);
            25
        }
    }
}
fn main(){
    let c= Coin::Quarter(UsState::Alaska);
    println!("{}",value_in_cents(c));
}
```

### 匹配Option<T>

```plain
fn main(){
    let five = Some(5);
    let six = plus_one(five);
    let none = plus_one(None); 
}
fn plus_one(x: Option<i32>) -> Option<i32> {
     match x {
        None => None,
        Some(i) => Some(i + 1),
     }
}//它获取一个 Option<i32> ，如果其中含有一个值，将其加一。如果其中没有值，函数应该返回 None 值，而不尝试执行任何操作。
```

### match匹配必须穷举所有的可能性

使用**_占位符**(必须放到最后面)

```plain
match dice_roll {
    3 => add_fancy_hat(),
    7 => remove_fancy_hat(),
    _ => reroll(),
}
```

## if let 简洁控制流

处理只关心一种模式匹配而忽略其它匹配的情况

```plain
let config_max = Some(3u8);
  match config_max {
        Some(max) => println!("The maximum is configured to be {}", max),
        _ => (),
    }
```

使用if let

```plain
let config_max = Some(3u8);
    if let Some(max) = config_max {
        println!("The maximum is configured to be {}", max);
    }
```

在这个例子中，模式是 Some(max)，max 绑定为 Some 中的值。接着可以在 if let 代码块中使用 max 了，就跟在对应的 match 分支中一样。模式不匹配时 if let 块中的代码不会执行。

放弃了穷举的可能性

可以把if let看作是match的语法糖

**搭配else使用**

```plain
let mut count = 0;
    if let Coin::Quarter(state) = coin {
        println!("State quarter from {:?}!", state);
    } else {
        count += 1;
    }
```

## 模式匹配中的变量遮蔽

```plain
fn main() {
    let age = Some(30);
    if let Some(age) = age { // 创建一个新的变量，该变量与之前的 `age` 变量同名
       assert_eq!(age, 30);
    } // 新的 `age` 变量在这里超出作用域
    
    match age {
        // `match` 也能实现变量遮蔽
        Some(age) =>  println!("age 是一个新的变量，它的值是 {}",age),
        _ => ()
    }
 }
//output:
//age 是一个新的变量，它的值是 30
```

# 九.Package,Crate,Module

- **包**（*Packages*）： Cargo 的一个功能，它允许你构建、测试和分享 crate。
- **Crate** ：一个模块的树形结构，它形成了库或二进制项目。
- **模块**（*Modules*）和 **use**： 允许你控制作用域和路径的私有性。
- **路径**（*path*）：一个命名例如结构体、函数或模块等项的方式

**Crate的类型**

- binary
- library

**Crate Root**

是源代码文件，Rust编译器从这里开始，组成你的Crate的根Module

**一个Package**

- 包含1个Cargo.toml，它描述了如何构建这些Crates
- 只能包含0-1个library crate
- 可以包含任意数量的binary crate
- 但必须至少包含一个crate（library或者binary)

## Cargo的惯例

src/main.rs

- binary crate的crate root
- crate名与package名相同

src/lib.rs

- package包含一个Library crate
- library crate的crate root
- crate名与package名相同

一个package可以同时包含src/main.rs和src/lib.rs

一个Package可以有多个binary crate：

文件放在src/bin下，每个文件都是单独的 binary crate

## 定义module来控制作用域和私有性

**Module**

- 在一个crate内，将diamagnetic进行分组
- 控制项目（item) 的私有性,public,private

**建立module**

- mod关键字
- 可嵌套
- 可包含其他项的定义(struct,enum,常量,trait，函数等)的定义

```plain
mod front_of_house {
    mod hosting {
        fn add_to_waitlist() {}

        fn seat_at_table() {}
    }

    mod serving {
        fn take_order() {}

        fn serve_order() {}

        fn take_payment() {}
    }
}
```

上述代码的模块树

```plain
crate
 └── front_of_house
     ├── hosting
     │   ├── add_to_waitlist
     │   └── seat_at_table
     └── serving
         ├── take_order
         ├── serve_order
         └── take_payment
```

src/main.rs和src/lib.rs叫做crate roots

- 这两个文件(任意一个)的内容形成了名为crate的模块，位于整个模块树的根部
- 整个模块树在隐式的crate模块下

## 路径

为了在Rust的模块中找到某个条目，需要使用**路径**

- **绝对路径**从crate root开始，使用crate名或字面值crate
- **相对路径**从当前模块开始，使用self，super或当前模块的标识符

路径至少由一个标识符组成，标识符之间使用::

src/lib.rs

```plain
mod front_of_house {
    mod hosting {
        fn add_to_waitlist() {}
    }
}

pub fn eat_at_restaurant() {//共有的
    // 绝对路径
    crate::front_of_house::hosting::add_to_waitlist();

    // 相对路径
    front_of_house::hosting::add_to_waitlist();
}
```

## 私有边界（privacy boundary)

- Rust的所有条目(函数，方法，struct,enum,模块,常量) **默认都是私有的**
- **父级模块无法访问所有子模块的私有条目**
- 子模块里可以使用**所有**祖先模块中的条目
- **同级模块**可以**互相调用**
- pub关键字可以标记为公共的

## Super

super:用来访问父级模块路径中的内容，类似于文件系统中的..

```plain
fn serve_order() {}

mod back_of_house {
    fn fix_incorrect_order() {
        cook_order();
        super::serve_order();
    }

    fn cook_order() {}
}
```

## pub struct

pub放在struct前：

- struct是公共的
- struct的字段默认是私有的，字段前面加pub就可以设为公有的

文件名: src/lib.rs

```plain
mod back_of_house {
    pub struct Breakfast {
        pub toast: String,
        seasonal_fruit: String,
    }

    impl Breakfast {
        pub fn summer(toast: &str) -> Breakfast {
            Breakfast {
                toast: String::from(toast),
                seasonal_fruit: String::from("peaches"),
            }
        }
    }
}

pub fn eat_at_restaurant() {
    // 在夏天订购一个黑麦土司作为早餐
    let mut meal = back_of_house::Breakfast::summer("Rye");
    // 改变主意更换想要面包的类型
    meal.toast = String::from("Wheat");
    println!("I'd like {} toast please", meal.toast);

    // 如果取消下一行的注释代码不能编译；
    // 不允许查看或修改早餐附带的季节水果
    // meal.seasonal_fruit = String::from("blueberries");
}
```

## pub enum

把pub放在enum前：

- enum是公共的
- enum的变体默认也都是公共的（不需要加pub关键字)

## use关键字

使用 use 关键字将路径引入作用域

```plain
mod front_of_house {
    pub mod hosting {
        pub fn add_to_waitlist() {}
    }
}

use crate::front_of_house::hosting;

pub fn eat_at_restaurant() {
    hosting::add_to_waitlist();
    hosting::add_to_waitlist();
    hosting::add_to_waitlist();
}
```

- 仍然遵循私有性规则
- 使用use来指定相对路径

## use的习惯用法

- 函数:将函数的父级模块引入到作用域(指定到父级)

文件名: src/lib.rs

```plain
mod front_of_house {
    pub mod hosting {
        pub fn add_to_waitlist() {}
    }
}

use self::front_of_house::hosting;

pub fn eat_at_restaurant() {
    hosting::add_to_waitlist();
    hosting::add_to_waitlist();
    hosting::add_to_waitlist();
}
```

- struct,enum,其他:指定完整路径(指定到本身)

文件名: src/main.rs

```plain
use std::collections::HashMap;

fn main() {
    let mut map = HashMap::new();
    map.insert(1, 2);
}
```

两个具有相同名称的项带入作用域

文件名: src/lib.rs

```plain
use std::fmt;
use std::io;

fn function1() -> fmt::Result {
    // --snip--
}

fn function2() -> io::Result<()> {
    // --snip--
}
```

## 使用 as 关键字提供新的名称

文件名: src/lib.rs

```plain
use std::fmt::Result;
use std::io::Result as IoResult;

fn function1() -> Result {
    // --snip--
}

fn function2() -> IoResult<()> {
    // --snip--
}
```

## pub use

使用use将路径（名称）导入到作用域内后，**该名称在此作用域内是私有的**

pub use：重导出

- 将条目引入到作用域
- 该条目可以被**外部代码**引入到它们的作用域

## pub(in Crate)

有时我们希望某一个项只对特定的包可见，那么就可以使用 pub(in Crate) 语法.

示例：

```plain
pub mod a {
    pub const I: i32 = 3;

    fn semisecret(x: i32) -> i32 {
        use self::b::c::J;
        x + J
    }

    pub fn bar(z: i32) -> i32 {
        semisecret(I) * z
    }
    pub fn foo(y: i32) -> i32 {
        semisecret(I) + y
    }

    mod b {
        pub(in crate::a) mod c {
            pub(in crate::a) const J: i32 = 4;
        }
    }
}
```

## 使用外部包(package)

1.Cargo.toml添加依赖的包

2.use将特定条目引入到作用域

- 标准库std也被当做外部包，但是不需要修改Cargo.toml来包含std
- 需要使用use将std中的特定条目引入当前作用域

## 使用嵌套路径清理大量的use语句

**路径相同的部分::{路径差异的部分}**

```plain
use std::{cmp::Ordering,io};
fn main()
```

如果两个use路径之一是另一个的子路径

使用self

```plain
//use std::io;
//use std::io::Write;
use std::io::{self,Write}
```

## 通配符*

使用*可以把路径中所有的公共条目都引入到作用域

谨慎使用

应用场景：

- prelude
- 测试，将所有被测试代码引入到tests模块

## 将模块拆分为不同的文件

模块定义时，如果模块名后面时";"，而不是代码块

- **Rust会从模块同名的文件中加载内容**
- **模块树不会发生变化**

示例:

文件名: src/lib.rs

```plain
mod front_of_house;

pub use crate::front_of_house::hosting;

pub fn eat_at_restaurant() {
    hosting::add_to_waitlist();
    hosting::add_to_waitlist();
    hosting::add_to_waitlist();
}
```

示例 ：声明 front_of_house 模块，其内容将位于 *src/front_of_house.rs*

*src/front_of_house.rs* 会获取 front_of_house 模块的定义内容，如示例所示。

文件名: src/front_of_house.rs

```plain
pub mod hosting {
    pub fn add_to_waitlist() {}
}
```

示例：在 *src/front_of_house.rs* 中定义 front_of_house 模块

把hosting也展开

文件名: src/front_of_house.rs

pub mod hosting;

接着我们创建一个 *src/front_of_house* 目录和一个包含 hosting 模块定义的 *src/front_of_house/hosting.rs* 文件：

文件名: src/front_of_house/hosting.rs

pub fn add_to_waitlist() {}

随着模块逐渐变大，该技术可以把模块的内容移动到其他文件中

# 十.常见集合

## Vector

Vec<T> 叫做vector

### 创建vector

**Vec::new函数**

let v:Vec<i32>=Vec::new();

使用初始值创建Vec<T>,使用**vec!**宏

let v = vec![1,2,3];

```plain
let arr: [u8; 3] = [1, 2, 3];
    
    let v = Vec::from(arr);
    is_vec(v);

    let v = vec![1, 2, 3];
    is_vec(v);

    // vec!(..) 和 vec![..] 是同样的宏，宏可以使用 []、()、{}三种形式，因此...
    let v = vec!(1, 2, 3);
    is_vec(v);
    
    // ...在下面的代码中, v 是 Vec<[u8; 3]> , 而不是 Vec<u8>
    let v1 = vec!(arr);
```

### 添加元素

```plain
let mut v:Vec<i32>=Vec::new();
v.push(1);
```

### 删除Vector

类似于任何其他的 struct，vector 在其离开作用域时会被释放

```plain
{
        let v = vec![1, 2, 3, 4];

        // 处理变量 v
    } // <- 这里 v 离开作用域并被丢弃
```

### 读取Vector中的值

- 索引方式
- get方法

```plain
let v = vec![1, 2, 3, 4, 5];

    let third: &i32 = &v[2];
    println!("The third element is {}", third);

    match v.get(2) {
        Some(third) => println!("The third element is {}", third),
        None => println!("There is no third element."),
    }
```

- **使用索引方法访问超出数组元素的值时，程序会panic**
- **而使用get方法访问时程序会返回一个None**

```plain
fn main() {
    let mut v = Vec::from([1, 2, 3]);
    for i in 0..5 {
        println!("{:?}", v.get(i))
    }

    for i in 0..5 {
        if let Some(x) = v.get(i) {
            v[i] = x + 1
        } else {
            v.push(i + 2)
        }
    }
    
    assert_eq!(format!("{:?}",v), format!("{:?}", vec![2, 3, 4, 5, 6]));

    println!("Success!")
}
```

### 引用借用规则

当我们获取了 vector 的第一个元素的不可变引用并尝试在 vector 末尾增加一个元素的时候，如果尝试在函数的后面引用这个元素是行不通的

```plain
let mut v = vec![1, 2, 3, 4, 5];

    let first = &v[0];//不可变的借用

    v.push(6);//可变的借用

    println!("The first element is: {}", first);//不可变的借用
```

为什么第一个元素的引用会关心 vector 结尾的变化？不能这么做的原因是由于 vector 的工作方式：在 vector 的结尾增加新元素时，在没有足够空间将所有元素依次相邻存放的情况下，可能会要求分配新内存并将老的元素拷贝到新的空间中。这时，第一个元素的引用就指向了被释放的内存。借用规则阻止程序陷入这种状况。

### 遍历Vector

```plain
let v = vec![100, 32, 57];
    for i in &v {
        println!("{}", i);
    }
```

我们也可以遍历可变 vector 的每一个元素的可变引用以便能改变他们

```plain
let mut v = vec![100, 32, 57];
    for i in &mut v {
        *i += 50;
    }
```

为了修改可变引用所指向的值，在使用 += 运算符之前必须使用解引用运算符（*）获取 i 中的值。

### 扩展Vector

Vec 可以使用 extend 方法进行扩展

```plain
fn main() {
    let mut v1 = Vec::from([1, 2, 4]);
    v1.pop();
    v1.push(3);
    
    let mut v2 = Vec::new();
    v2.extend([1, 2, 3]);

    assert_eq!(format!("{:?}",v1), format!("{:?}",v2));

    println!("Success!")
}
```

### 使用enum来使Vec存储多种数据类型

定义一个枚举，以便能在 vector 中存放不同类型的数据

```plain
enum SpreadsheetCell {
        Int(i32),
        Float(f64),
        Text(String),
    }

    let row = vec![
        SpreadsheetCell::Int(3),
        SpreadsheetCell::Text(String::from("blue")),
        SpreadsheetCell::Float(10.12),
    ];
```

## 使用特征对象来使Vec存储多种数据类型

```plain
trait IpAddr {
    fn display(&self);
}

struct V4(String);
impl IpAddr for V4 {
    fn display(&self) {
        println!("ipv4: {:?}",self.0)
    }
}
struct V6(String);
impl IpAddr for V6 {
    fn display(&self) {
        println!("ipv6: {:?}",self.0)
    }
}

fn main() {
    // 填空
    let v: Vec<Box<dyn IpAddr>> = vec![
        Box::new(V4("127.0.0.1".to_string())),
        Box::new(V6("::1".to_string())),
    ];

    for ip in v {
        ip.display();
    }
}
```

### 将 X 类型转换(From/Into 特征)成 Vec

```plain
fn main() {
    // array -> Vec
    let arr = [1, 2, 3];
    let v1 = Vec::from(arr);
    let v2: Vec<i32> = arr.into();
 
    assert_eq!(v1, v2);
 
    
    // String -> Vec
    let s = "hello".to_string();
    let v1: Vec<u8> = s.into();

    let s = "hello".to_string();
    let v2 = s.into_bytes();
    assert_eq!(v1, v2);

    let s = "hello";
    let v3 = Vec::from(s);
    assert_eq!(v2, v3);

    println!("Success!")
 }
```

### 切片

与 String 的切片类似， Vec 也可以使用切片。如果说 Vec 是可变的，那它的切片就是不可变或者说只读的，我们可以通过 & 来获取切片。

在 Rust 中，将切片作为参数进行传递是更常见的使用方式，例如当一个函数只需要可读性时，那传递 Vec 或 String 的切片 &[T] / &str 会更加适合。

```plain
fn main() {
    let mut v = vec![1, 2, 3];

    let slice1 = &v[..];
    // 越界访问将导致 panic.
    // 修改时必须使用 `v.len`
    let slice2 = &v[0..v.len()];
    
    assert_eq!(slice1, slice2);
    
    // 切片是只读的
    // 注意：切片和 `&Vec` 是不同的类型，后者仅仅是 `Vec` 的引用，并可以通过解引用直接获取 `Vec`
    let vec_ref: &mut Vec<i32> = &mut v;
    (*vec_ref).push(4);
    let slice3 = &mut v[0..];
    // slice3.push(4);

    assert_eq!(slice3, &[1, 2, 3, 4]);

    println!("Success!")
}
```

### 容量

容量 capacity 是已经分配好的内存空间，用于存储未来添加到 Vec 中的元素。而长度 len 则是当前 Vec 中已经存储的元素数量。如果要添加新元素时，长度将要超过已有的容量，那容量会自动进行增长：Rust 会重新分配一块更大的内存空间，然后将之前的 Vec 拷贝过去，因此，这里就会发生新的内存分配

若这段代码会频繁发生，那频繁的内存分配会大幅影响我们系统的性能，最好的办法就是提前分配好足够的容量，尽量减少内存分配。

```plain
fn main() {
    let mut vec = Vec::with_capacity(10);

    assert_eq!(vec.len(), 0);
    assert_eq!(vec.capacity(), 10);

    // 由于提前设置了足够的容量，这里的循环不会造成任何内存分配...
    for i in 0..10 {
        vec.push(i);
    }
    assert_eq!(vec.len(), 10);
    assert_eq!(vec.capacity(), 10);

    // ...但是下面的代码会造成新的内存分配
    vec.push(11);
    assert_eq!(vec.len(), 11);
    assert!(vec.capacity() >= 11);


    // 填写一个合适的值，在 `for` 循环运行的过程中，不会造成任何内存分配
    let mut vec = Vec::with_capacity(100);
    for i in 0..100 {
        vec.push(i);
    }

    assert_eq!(vec.len(), 100);
    assert_eq!(vec.capacity(), 100);
    
    println!("Success!")
}
```

## String只要为 Vec 实现了 From<T> 特征，那么 T 就可以被转换成 Vec。

- 字符串是Byte的集合
- UFT-8编码
- 一些方法能将byte解析为文本

### 字符串是什么？

Rust的核心语言层面，只有一个字符串类型:字符串切片str（或者&str）

- 字符串切片:对存储在其它地方,UTF-8编码的字符串引用
- 字符串的字面值：存储在二进制文件中，也是字符串切片

```plain
fn main() {
    let s = String::from("hello, 世界");
    let slice1 = &s[0..1]; 
    //提示: `h` 在 UTF-8 编码中只占用 1 个字节
    assert_eq!(slice1, "h");

    let slice2 = &s[7..10];// 提示: `中` 在 UTF-8 编码中占用 3 个字节
    assert_eq!(slice2, "世");
    
    // 迭代 s 中的所有字符
    for (i, c) in s.chars().enumerate() {
        if i == 7 {
            assert_eq!(c, '世')
        }
    }

    println!("Success!")
}
```

事实上 String 是一个**智能指针**，它作为一个结构体存储在栈上，然后指向存储在堆上的字符串底层数据。

存储在栈上的智能指针结构体由三部分组成：一个指针只指向堆上的字节数组，已使用的长度以及已分配的容量 capacity (已使用的长度小于等于已分配的容量，当容量不够时，会重新分配内存空间)。

```plain
use std::mem;

fn main() {
    let story = String::from("Rust By Practice");

    // 阻止 String 的数据被自动 drop
    let mut story = mem::ManuallyDrop::new(story);

    let ptr = story.as_mut_ptr();
    let len = story.len();
    let capacity = story.capacity();

    assert_eq!(16, len);

    // 我们可以基于 ptr 指针、长度和容量来重新构建 String. 
    // 这种操作必须标记为 unsafe，因为我们需要自己来确保这里的操作是安全的
    let s = unsafe { String::from_raw_parts(ptr, len, capacity) };

    assert_eq!(*story, s);

    println!("Success!")
}
```

**String类型**

来自标准库，也是UTF-8编码

**其它字符串类型**

OsString,OsStr,CString,CStr等等

String vs Str：拥有或借用的变体

可存储不同编码的文本或在内存中以不同的形式展现

Library crate针对存储字符串提供了共多的选项

### String与&str的转换

```plain
fn main() {
    let s = "hello, world".to_string();
    greetings(s)
}

fn greetings(s: String) {
    println!("{}",s)
}
fn main() {
    let s = String::from("hello, world");
    greetings(s)
}

fn greetings(s: String) {
    println!("{}",s)
}
```

### 字符串转义

```plain
fn main() {
    // 你可以使用转义的方式来输出想要的字符，这里我们使用十六进制的值，例如 \x73 会被转义成小写字母 's'
    // 填空以输出 "I'm writing Rust"
    let byte_escape = "I'm writing Ru\x73__!";
    println!("What are you doing\x3F (\\x3F means ?) {}", byte_escape);

    // 也可以使用 Unicode 形式的转义字符
    let unicode_codepoint = "\u{211D}";
    let character_name = "\"DOUBLE-STRUCK CAPITAL R\"";

    println!("Unicode character {} (U+211D) is called {}",
                unicode_codepoint, character_name );

    // 还能使用 \ 来连接多行字符串
    let long_string = "String literals
                        can span multiple lines.
                        The linebreak and indentation here \
                         can be escaped too!";
    println!("{}", long_string);
}
```

**有时候需要转义的字符很多，我们会希望使用更方便的方式来书写字符串: raw string.**

```plain
fn main() {
    let raw_str = r"Escapes don't work here: \x3F \u{211D}";
    println!("{}", raw_str);

    // 如果字符串包含双引号，可以在开头和结尾加 #
    let quotes = r#"And then I said: "There is no escape!""#;
    println!("{}", quotes);

    // 如果还是有歧义，可以继续增加，没有限制
    let longer_delimiter = r###"A string with "# in it. And even "##!"###;
    println!("{}", longer_delimiter);
}
fn main() {
    let raw_str = "Escapes don't work here: \x3F \u{211D}";
    assert_eq!(raw_str, "Escapes don't work here: ? ℝ");

    // If you need quotes in a raw string, add a pair of #s
    let quotes = r#"And then I said: "There is no escape!""#;
    println!("{}", quotes);

    // If you need "# in your string, just use more #s in the delimiter.
    // You can use up to 65535 #s.
    let delimiter = r###"A string with "# in it. And even "##!"###;
    println!("{}", delimiter);

    // Fill the blank
    let long_delimiter = r###"Hello, "##""###;
    assert_eq!(long_delimiter, "Hello, \"##\"")
}
```

这里r#"标记一个原始字符串的开始，"#标记一个字符串的结束，如果还是有歧义可以继续加#

### 创建一个新的String

String::new()

let mut s = String::new();

使用to_string()

```plain
let data = "initial contents";

    let s = data.to_string();

    // 该方法也可直接用于字符串字面值：
    let s = "initial contents".to_string();
```

string::from()

let s = String::from("initial contents");

### 更新String

#### **push_str()**

```plain
let mut s = String::from("foo");
    s.push_str("bar");
```

push_str()方法不会获得参数的所有权

```plain
let mut s1 = String::from("foo");
    let s2 = "bar";
    s1.push_str(s2);
    println!("s2 is {}", s2);
let mut s1 = String::from("foo");
    let s2 = "bar";
    s1.push_str(&s2);
    println!("s2 is {}", s2);
```

#### **push()**

push 方法被定义为获取一个单独的字符作为参数，并附加到 String 中

```plain
let mut s = String::from("lo");
    s.push('l');
```

#### **+连接字符串**

**只能将** **String** **跟** **&str** **类型进行拼接，并且** **String** **的所有权在此过程中会被 move**

```plain
let s1 = String::from("Hello, ");
    let s2 = String::from("world!");
    let s3 = s1 + &s2; // 注意 s1 被移动了，不能继续使用
```

\+ 运算符使用了 add 函数，这个函数签名看起来像这样：

fn add(self, s: &str) -> String {

这并不是标准库中实际的签名；

但是&s2 的类型是 &String 而不是 &str。那么为什么还能编译呢

之所以能够在 add 调用中使用 &s2 是因为 &String 可以被 **强转**（*coerced*）成 &str。当add函数被调用时，Rust 使用了一个被称为 **Deref 强制转换**（*deref coercion*）的技术，你可以将其理解为它把 &s2 变成了 &s2[..]。

#### format!

```plain
let s1 = String::from("tic");
    let s2 = String::from("tac");
    let s3 = String::from("toe");

    let s = s1 + "-" + &s2 + "-" + &s3;
let s1 = String::from("tic");
    let s2 = String::from("tac");
    let s3 = String::from("toe");

    let s = format!("{}-{}-{}", s1, s2, s3);
```

宏 format! 生成的代码使用引用所以**不会获取任何参数的所有权**

### 按索引的形式进行访问**String**

Rust的字符串**不支持索引语法**访问

**内部表现**

String 是一个 Vec<u8> 的封装。

​    let hello = String::from("Hola");

在这里，len 的值是 4 ，这意味着储存字符串 “Hola” 的 Vec 的长度是四个字节：这里每一个字母的 UTF-8 编码都占用一个字节。

（注意这个字符串中的首字母是西里尔字母的 Ze 而不是阿拉伯数字 3 。）

​    let hello = String::from("Здравствуйте");

当问及这个字符是多长的时候有人可能会说是 12。然而，Rust 的回答是 24。这是使用 UTF-8 编码 “Здравствуйте” 所需要的字节数，这是因为每个 Unicode 标量值需要两个字节存储。

**因此一个字符串字节值的索引并不总是对应一个有效的 Unicode 标量值**

### 字节字符串

字节字符串或者说字节数组

```plain
use std::str;

fn main() {
    // 注意，这并不是 `&str` 类型了！
    let bytestring: &[u8; 21] = b"this is a byte string";


    // 字节数组没有实现 `Display` 特征，因此只能使用 `Debug` 的方式去打印
    println!("A byte string: {:?}", bytestring);

    // 字节数组也可以使用转义
    let escaped = b"\x52\x75\x73\x74 as bytes";
    // ...但是不支持 unicode 转义
    // let escaped = b"\u{211D} is not allowed";
    println!("Some escaped bytes: {:?}", escaped);


    // raw string
    let raw_bytestring = br"\u{211D} is not escaped here";
    println!("{:?}", raw_bytestring);

    // 将字节数组转成 `str` 类型可能会失败
    if let Ok(my_str) = str::from_utf8(raw_bytestring) {
        println!("And the same as text: '{}'", my_str);
    }

    let _quotes = br#"You can also use "fancier" formatting, \
                    like with normal raw strings"#;

    // 字节数组可以不是 UTF-8 格式
    let shift_jis = b"\x82\xe6\x82\xa8\x82\xb1\x82\xbb"; // "ようこそ" in SHIFT-JIS

    // 但是它们未必能转换成 `str` 类型
    match str::from_utf8(shift_jis) {
        Ok(my_str) => println!("Conversion successful: '{}'", my_str),
        Err(e) => println!("Conversion failed: {:?}", e),
    };
}
```

### 字节,标量值,字型簇

Rust有三种看待字符串的方式：

- 字节Byte

```plain
fn main(){
    let w = "नमस्ते";
    for b in w.bytes(){
        println!("{}",b);
    }
}
```

输出

```plain
224
164
168
224
164
174
224
164
184
224
165
141
224
164
164
224
165
135
```

- 标量值Scalar Values

```plain
fn main(){
    let w = "नमस्ते";
    for b in w.chars(){
        println!("{}",b);
    }
}
```

输出

```plain
न
म
स
्
त
े
```

- 字形簇Grapheme Clusters（最接近所谓的字母）

获取比较复杂，标准库中已经不提供了

在crates.io网站上可以找到

Rust不允许对String进行索引的最后一个原因:

索引操作应该小号一个常量时间O(1)

而String无法保证：需要遍历所有的内容，来确定有多少个合法的字符

### 切割String

字符串索引应该返回的类型是不明确的：字节值、字符、字形簇或者字符串 slice。因此，如果你真的希望使用索引创建字符串 slice 时，Rust 会要求你更明确一些。为了更明确索引并表明你需要一个字符串 slice，相比使用 [] 和单个值的索引，可以使用 [] 和一个 range 来创建含特定字节的字符串 slice：

```plain
let hello = "Здравствуйте";

let s = &hello[0..4];
```

s 会是一个 &str，它包含字符串的头四个字节。早些时候，我们提到了这些字母都是两个字节长的，所以这意味着 s 将会是 “Зд”。

如果获取 &hello[0..1] 会发生什么呢？答案是：Rust 在运行时会 panic

因此**切割时不能跨越字符串边界**

### 遍历字符串

无法通过索引的方式去访问字符串中的某个字符，但是可以使用切片的方式 &s1[start..end] ，但是start 和 end 必须准确落在字符的边界处.

- 对于标量值:chars()方法
- 对于字节:bytes()方法、
- 对于字形簇:很复杂，标准库未提供，中英文都不需要关注字符簇

```plain
fn main() {
    let s1 = String::from("hi,中国");
    let h = &s1[0..1]; 
    assert_eq!(h, "h");

    let h1 = &s1[3..6];
    assert_eq!(h1, "中");
}
fn main() {
    for c in "你好，世界".chars() {
        println!("{}", c)
    }
}
//output:
//你
//好
//，
//世
//界
```

我们可以使用三方库 [utf8_slice]来访问 UTF-8 字符串的某个子串，但是与之前不同的是，该库索引的是字符，而不是字节.

```plain
[dependencies]
utf8_slice = "1.0.0"
fn main() {
    let s = "The 🚀 goes to the 🌑!";

    let rocket = utf8_slice::slice(s, 4, 5);
    // 结果是 "🚀"
    println!("{}",rocket);
}
```

## HashMap

HashMap 默认使用 SipHash 1-3 哈希算法，该算法对于抵抗 HashDos 攻击非常有效。在性能方面，如果你的 key 是中型大小的，那该算法非常不错，但是如果是小型的 key( 例如整数 )亦或是大型的 key ( 例如字符串 )，那你需要采用社区提供的其它算法来提高性能。

哈希表的算法是基于 Google 的 [SwissTable](https://abseil.io/blog/20180927-swisstables)，你可以在[这里](https://github.com/abseil/abseil-cpp/blob/master/absl/container/internal/raw_hash_set.h)找到 C++ 的实现，同时在 [CppCon talk](https://www.youtube.com/watch?v=ncHmEUmJZf4) 上也有关于算法如何工作的演讲。

### 创建HashMap<K,V>

**创建空HashMap:new()函数**

```plain
use std::collections::HashMap;

    let mut scores = HashMap::new();
//let mut scores:HashMap<String,i32> = HashMap::new();

    scores.insert(String::from("Blue"), 10);
    scores.insert(String::from("Yellow"), 50);
```

- HashMap用的较少，不在Prelude中
- 标准库对其支持较少，没有内置的宏来创建HashMap
- 数据存在heap中
- 同构的，即K必须为一种类型，V为另一种类型

```plain
use std::collections::HashMap;
fn main() {
    let teams = [
        ("Chinese Team", 100),
        ("American Team", 10),
        ("France Team", 50),
    ];

    let mut teams_map1 = HashMap::new();
    for team in &teams {
        teams_map1.insert(team.0, team.1);
    }

    let teams_map2: HashMap<_,_> = teams.into_iter().collect();
    // let teams_map2 = HashMap::from(teams);
    assert_eq!(teams_map1, teams_map2);

    println!("Success!")
}
```

**collect方法创建HashMap**

collect方法可以将数据收集进一系列的集合类型

```plain
use std::collections::HashMap;

    let teams = vec![String::from("Blue"), String::from("Yellow")];
    let initial_scores = vec![10, 50];

    let mut scores: HashMap<_, _> =
        teams.into_iter().zip(initial_scores.into_iter()).collect();
```

如果队伍的名字和初始分数分别在两个 vector 中，可以使用 zip 方法来创建一个元组的迭代器，其中 “Blue” 与 10 是一对，依此类推。接着就可以使用 collect 方法将这个元组的迭代器转换成一个 HashMap

### HashMap和所有权

- 对于实现了Copy trait的类型（如i32)，值会被复制到HashMap中
- 对于拥有所有权的值（例如String)，值会被移动，所有权会转移给HashMap
- 如果把引用插入到HashMap，值本身不会移动但是在HashMap有效的期间，被引用的值必须保持有效

### 访问HashMap中的值

**get方法**

```plain
use std::collections::HashMap;

    let mut scores = HashMap::new();

    scores.insert(String::from("Blue"), 10);
    scores.insert(String::from("Yellow"), 50);

    let team_name = String::from("Blue");
    let score = scores.get(&team_name);
```

**for循环遍历HashMap**

```plain
use std::collections::HashMap;

    let mut scores = HashMap::new();

    scores.insert(String::from("Blue"), 10);
    scores.insert(String::from("Yellow"), 50);

    for (key, value) in &scores {
        println!("{}: {}", key, value);
    }
```

这会以**任意顺序**打印出每一个键值对：

```plain
Yellow: 50
Blue: 10
```

**索引与get方法**

```plain
use std::collections::HashMap;
fn main() {
    let mut scores = HashMap::new();
    scores.insert("Sunface", 98);
    scores.insert("Daniel", 95);
    scores.insert("Ashley", 69);
    scores.insert("Katie", 58);

    // get 返回一个 Option<&V> 枚举值
    let score = scores.get("Sunface");
    assert_eq!(score, Some(&98));

    if scores.contains_key("Daniel") {
        // 索引返回一个值 V
        let score = scores["Daniel"];
        assert_eq!(score, 95);
        scores.remove("Daniel");
    }

    assert_eq!(scores.len(), 3);

    for (name, score) in scores {
        println!("The score of {} is {}", name, score)
    }
}
```

### 更新HashMap

#### 覆盖一个值

```plain
use std::collections::HashMap;

    let mut scores = HashMap::new();

    scores.insert(String::from("Blue"), 10);
    scores.insert(String::from("Blue"), 25);

    println!("{:?}", scores);
```

这会打印出 {"Blue": 25}。原始的值 10 则被覆盖了

#### 只在键没有对应值时插入

使用 entry 方法只在键没有对应一个值时插入

```plain
use std::collections::HashMap;

    let mut scores = HashMap::new();
    scores.insert(String::from("Blue"), 10);

	let e=scoress.entry(String::from("Yellow"));
	println!("{:?}",e)
	e.or_insert(50);
    scores.entry(String::from("Blue")).or_insert(50);

    println!("{:?}", scores);
```

Entry 的 or_insert 方法在键对应的值存在时就返回

**entry方法**

检查指定的K是否对应一个V

参数为K,返回enum Entry:代表值是否存在

Entry的**or_insert()方法**:

返回:

- 如果K存在，返回到对应的V的一个可变引用
- 如果K不存在，将方法参数作为K的新值插进去，返回到这个值的可变引用

#### 根据旧值更新一个值

```plain
use std::collections::HashMap;

    let text = "hello world wonderful world";

    let mut map = HashMap::new();

    for word in text.split_whitespace() {
        let count = map.entry(word).or_insert(0);
        *count += 1;
    }

    println!("{:?}", map);
```

这里or_insert返回的是一个可变引用

#### 哈希函数

HashMap 默认使用一种叫做 SipHash 的哈希函数，它可以抵御涉及哈希表（hash table）[1](https://kaisery.github.io/trpl-zh-cn/ch08-03-hash-maps.html#siphash) 的拒绝服务（Denial of Service, DoS）攻击。然而这并不是可用的最快的算法，不过为了更高的安全性值得付出一些性能的代价。如果性能监测显示此哈希函数非常慢，以致于你无法接受，你可以指定一个不同的 *hasher* 来切换为其它函数。hasher 是一个实现了 BuildHasher trait 的类型。第十章会讨论 trait 和如何实现它们。你并不需要从头开始实现你自己的 hasher；[crates.io](https://crates.io/) 有其他人分享的实现了许多常用哈希算法的 hasher 的库。

### HashMap key的限制

任何实现了 Eq 和 Hash 特征的类型都可以用于 HashMap 的 key，包括:

- bool (虽然很少用到，因为它只能表达两种 key)
- int, uint 以及它们的变体，例如 u8、i32 等
- String 和 &str (提示: HashMap 的 key 是 String 类型时，你其实可以使用 &str 配合 get 方法进行查询

需要注意的是，f32 和 f64 并没有实现 Hash，原因是 [浮点数精度](https://en.wikipedia.org/wiki/Floating-point_arithmetic#Accuracy_problems) 的问题会导致它们无法进行相等比较。

如果一个集合类型的所有字段都实现了 Eq 和 Hash,那该集合类型会自动实现 Eq 和 Hash。例如 Vect<T> 要实现 Hash，那么首先需要 T 实现 Hash。

```plain
// 提示: `derive` 是实现一些常用特征的好办法
use std::collections::HashMap;

#[derive(Hash, Eq, PartialEq, Debug)]
struct Viking {
    name: String,
    country: String,
}

impl Viking {
    fn new(name: &str, country: &str) -> Viking {
        Viking {
            name: name.to_string(),
            country: country.to_string(),
        }
    }
}

fn main() {
    // 使用 HashMap 来存储 viking 的生命值
    let vikings = HashMap::from([
        (Viking::new("Einar", "Norway"), 25),
        (Viking::new("Olaf", "Denmark"), 24),
        (Viking::new("Harald", "Iceland"), 12),
    ]);

    // 使用 derive 的方式来打印 viking 的当前状态
    for (viking, health) in &vikings {
        println!("{:?} has {} hp", viking, health);
    }
}
```

### 容量

关于容量，我们在之前的 Vector中有详细的介绍，而 HashMap 也可以调整容量: 你可以通过 HashMap::with_capacity(uint) 使用指定的容量来初始化，或者使用 HashMap::new() ，后者会提供一个默认的初始化容量。

```plain
use std::collections::HashMap;
fn main() {
    let mut map: HashMap<i32, i32> = HashMap::with_capacity(100);
    map.insert(1, 2);
    map.insert(3, 4);
    // 事实上，虽然我们使用了 100 容量来初始化，但是 map 的容量很可能会比 100 更多
    assert!(map.capacity() >= 100);

    // 对容量进行收缩，你提供的值仅仅是一个允许的最小值，实际上，Rust 会根据当前存储的数据量进行自动设置，当然，这个值会尽量靠近你提供的值，同时还可能会预留一些调整空间

    map.shrink_to(50);
    assert!(map.capacity() >= 50);

    // 让 Rust  自行调整到一个合适的值，剩余策略同上
    map.shrink_to_fit();
    assert!(map.capacity() >= 2);
    println!("Success!")
}
```

### 所有权

对于实现了 Copy 特征的类型，例如 i32，那类型的值会被拷贝到 HashMap 中。而对于有所有权的类型，例如 String，它们的值的所有权将被转移到 HashMap 中。

```plain
// 修复错误，尽可能少的去修改代码
// 不要移除任何代码行！
use std::collections::HashMap;
fn main() {
  let v1 = 10;
  let mut m1 = HashMap::new();
  m1.insert(v1, v1);
  println!("v1 is still usable after inserting to hashmap : {}", v1);

  let v2 = "hello".to_string();
  let mut m2 = HashMap::new();
  // 所有权在这里发生了转移
  m2.insert(v2, v1);

  assert_eq!(v2, "hello");

   println!("Success!")
}
```

### 第三方Hash库

在开头，我们提到过如果现有的 SipHash 1-3 的性能无法满足你的需求，那么可以使用社区提供的替代算法。

例如其中一个社区库的使用方式如下：

```plain
use std::hash::BuildHasherDefault;
use std::collections::HashMap;
// 引入第三方的哈希函数
use twox_hash::XxHash64;


let mut hash: HashMap<_, _, BuildHasherDefault<XxHash64>> = Default::default();
hash.insert(42, "the answer");
assert_eq!(hash.get(&42), Some(&"the answer"));
```

# 十一.错误处理

大部分情况下，在编译时提示错误，并处理

**错误的分类**

- 可恢复:

例如文件未找到，可再次尝试

- 不可恢复

bug,例如访问索引超出范围

**Rust没有类似异常的机制**

- 可恢复的错误:Result<T,E>
- 不可恢复:panic!宏

## 不可恢复的错误与panic!

当panic!宏执行

- **程序打印一个错误信息**
- **展开(unwind),清理调用栈(Stack)**
- **退出程序**

## 为应对panic,展开或中止(abort)调用栈

默认情况下，当panic发生

- 程序展开调用栈(工作量大)

Rust沿着调用栈往回走

清理每个遇到的函数中的数据

- 或立即中止调用栈

不进行清理，直接停止程序

内存需要由OS进行清理

**想让二进制文件更小，把设置从“展开”改为“中止”**

```plain
[profile.release]
panic = 'abort'
```

自己写的代码中内panic

```plain
fn main(){
    panic!("crash and burn");
}
```

所以依赖的代码中：外部panic

```plain
fn main(){
    let vector=vec![1,2,3];
    vector[100];
}
```

输出

```plain
Compiling demo v0.1.0 (C:\Users\cauchy\Desktop\rust\demo)
    Finished dev [unoptimized + debuginfo] target(s) in 0.18s
     Running `target\debug\demo.exe`
thread 'main' panicked at 'index out of bounds: the len is 3 but the index is 100', src\main.rs:3:5
note: run with `RUST_BACKTRACE=1` environment variable to display a backtrace
error: process didn't exit successfully: `target\debug\demo.exe` (exit code: 101)
```

### 通过调用panic!的函数的回溯信息来定位引起问题的代码

使用以下命令运行代码(Windows cmd)

set RUST_BACKTRACE=1 && cargo run

Windows Poweshell 

$Env:RUST_BACKTRACE=1 -and (cargo run)

linux下

export RUST_BACKTRACE=1 && cargo run

更详细的信息

**RUST_BACKTRACE=full**

为了获取带有调试信息的回溯，必须启用调试符号(不带--release)

## Result枚举与可恢复的错误

```plain
enum Result<T, E> {
    Ok(T),
    Err(E),
}
```

T 代表成功时返回的 Ok 成员中的数据的类型，

而 E 代表失败时返回的 Err 成员中的错误的类型

Result及其变体也是由prelude带入作用域的

打开文件

```plain
use std::fs::File;

fn main() {
    let f = File::open("hello.txt");

    let f = match f {
        Ok(file) => file,
        Err(error) => panic!("Problem opening the file: {:?}", error),
    };
}
```

### 匹配不同的错误

```plain
use std::fs::File;
use std::io::ErrorKind;

fn main() {
    let f = File::open("hello.txt");

    let f = match f {
        Ok(file) => file,
        Err(error) => match error.kind() {
            ErrorKind::NotFound => match File::create("hello.txt") {
                Ok(fc) => fc,
                Err(e) => panic!("Problem creating the file: {:?}", e),
            },
            other_error => {
                panic!("Problem opening the file: {:?}", other_error)
            }
        },
    };
}
```

闭包（closure）

```plain
use std::fs::File;
use std::io::ErrorKind;

fn main() {
    let f = File::open("hello.txt").unwrap_or_else(|error| {
        if error.kind() == ErrorKind::NotFound {
            File::create("hello.txt").unwrap_or_else(|error| {
                panic!("Problem creating the file: {:?}", error);
            })
        } else {
            panic!("Problem opening the file: {:?}", error);
        }
    });
}
```

### unwrap

match表达式的一个快捷方法

```plain
use std::fs::File;

fn main() {
    let f = File::open("hello.txt").unwrap();
}
```

相当于

```plain
use std::fs::File;

fn main() {
    let f = File::open("hello.txt");

    let f = match f {
        Ok(file) => file,
        Err(error) => panic!("Problem opening the file: {:?}", error),
    };
}
```

- 如果Result结果是Ok，返回Ok里面的值
- 如果Result结果是Err,调用panic!宏

### expect

可自定义错误信息的unwrap

```plain
use std::fs::File;

fn main() {
    let f = File::open("hello.txt").expect("无法打开文件");
}
```

## 传播错误

将错误传播给调用者

**自定义实现**

```plain
// src/main.rs

use std::fs::File;
use std::io::{self, Read};

fn read_username_from_file() -> Result<String, io::Error> {
    let f = File::open("hello.txt");

    let mut f = match f {
        Ok(file) => file,
        Err(e) => return Err(e),
    };

    let mut s = String::new();

    match f.read_to_string(&mut s) {
        Ok(_) => Ok(s),
        Err(e) => Err(e),
    }
}
```

## ？运算符

```plain
use std::fs::File;
use std::io;
use std::io::Read;

fn read_username_from_file() -> Result<String, io::Error> {
    let mut f = File::open("hello.txt")?;
    let mut s = String::new();
    f.read_to_string(&mut s)?;
    Ok(s)
}
```

? 被定义为与自定义传播错误的示例中定义的处理 Result 值的 match 表达式有着完全相同的工作方式。

如果 Result 的值是 Ok，这个表达式将会返回 Ok 中的值而程序将继续执行。

如果值是 Err，Err 中的值将作为整个函数的返回值，**就好像使用了** **return** **关键字一样**，这样错误值就被传播给了调用者。

```plain
use std::fs::File;
use std::io::{self, Read};

fn read_file1() -> Result<String, io::Error> {
    let f = File::open("hello.txt");
    let mut f = match f {
        Ok(file) => file,
        Err(e) => return Err(e),
    };
    
    let mut s = String::new();
    match f.read_to_string(&mut s) {
        Ok(_) => Ok(s),
        Err(e) => Err(e),
    }
}

fn read_file2() -> Result<String, io::Error> {
    let mut s = String::new();

    File::open("hello.txt")?.read_to_string(&mut s)?;

    Ok(s)
}

fn main() {
    assert_eq!(read_file1().unwrap_err().to_string(), read_file2().unwrap_err().to_string());
    println!("Success!")
}
```

## ？与 from 函数

Trait std::convert::From 上的from函数

- 用于错误之间的转换
- 被？所应用的错误，会隐式地被from 函数处理
- 当? 调用from 函数时:

它所接收地错误类型会被转化为当前函数返回类型所定义的错误类型

用于:针对不同的错误原因，返回同一种错误类型

只要每个错误类型实现了转换为所返回的错误类型的 from 函数

可以在 ? 之后直接使用**链式方法调用**来进一步缩短代码

文件名: src/main.rs

```plain
use std::fs::File;
use std::io;
use std::io::Read;

fn read_username_from_file() -> Result<String, io::Error> {
    let mut s = String::new();

    File::open("hello.txt")?.read_to_string(&mut s)?;

    Ok(s)
}
```

## ？与main函数

main函数的返回类型为()类型

```plain
use std::error::Error;
use std::fs::File;

fn main() -> Result<(), Box<dyn Error>> {
    let f = File::open("hello.txt")?;
    Ok(())
}
```

## map，and_then

```plain
use std::num::ParseIntError;

// With the return type rewritten, we use pattern matching without `unwrap()`.
// But it's so Verbose..
fn multiply(n1_str: &str, n2_str: &str) -> Result<i32, ParseIntError> {
    match n1_str.parse::<i32>() {
        Ok(n1)  => {
            match n2_str.parse::<i32>() {
                Ok(n2)  => {
                    Ok(n1 * n2)
                },
                Err(e) => Err(e),
            }
        },
        Err(e) => Err(e),
    }
}

// Rewriting `multiply` to make it succinct
// You  MUST USING `and_then` and `map` here
fn multiply1(n1_str: &str, n2_str: &str) -> Result<i32, ParseIntError> {
    // IMPLEMENT...
    n1_str.parse::<i32>().and_then(|n1| {
        n2_str.parse::<i32>().map(|n2| n1 * n2)
    })
}

fn print(result: Result<i32, ParseIntError>) {
    match result {
        Ok(n)  => println!("n is {}", n),
        Err(e) => println!("Error: {}", e),
    }
}

fn main() {
    // This still presents a reasonable answer.
    let twenty = multiply1("10", "2");
    print(twenty);

    // The following now provides a much more helpful error message.
    let tt = multiply("t", "2");
    print(tt);

    println!("Success!")
}
```

## 何时panic!

### **总体原则**

- 在定义一个可能失败的函数时，优先考虑返回Result
- 否则就panic!

### 场景

- 演示某些概念：unwrap
- 原型代码:unwrap,expect
- 测试:unwrap,expect

**有时你比编译器掌握更多的信息**

你可以确定Result就是Ok:**unwrap**

```plain
use std::net::IpAddr;
  let home: IpAddr = "127.0.0.1".parse().unwrap();
```

调用你的代码，传入无意义的参数值:**panic!**

调用外部不可控代码，返回非法状态，你无法修复:**panic!**

如果失败是可预期的：**Result**

当你的代码对值进行操作，首先应该验证这些值:**panic!**

### 创建自定义类型进行有效性验证

一种实现方式是将猜测解析成 i32 而不仅仅是 u32，来默许输入负数，接着检查数字是否在范围内：

```plain
loop {
        // --snip--

        let guess: i32 = match guess.trim().parse() {
            Ok(num) => num,
            Err(_) => continue,
        };

        if guess < 1 || guess > 100 {
            println!("The secret number will be between 1 and 100.");
            continue;
        }

        match guess.cmp(&secret_number) {
            // --snip--
    }
```

if 表达式检查了值是否超出范围，告诉用户出了什么问题，并调用 continue 开始下一次循环，请求另一个猜测。if 表达式之后，就可以在知道 guess 在 1 到 100 之间的情况下与秘密数字作比较了。

相反我们可以创建一个新类型来将验证放入创建其实例的函数中，而不是到处重复这些检查。这样就可以安全的在函数签名中使用新类型并相信他们接收到的值。示例 中展示了一个定义 Guess 类型的方法，只有在 new 函数接收到 1 到 100 之间的值时才会创建 Guess 的实例：

```plain
pub struct Guess {
    value: i32,
}

impl Guess {
    pub fn new(value: i32) -> Guess {
        if value < 1 || value > 100 {
            panic!("Guess value must be between 1 and 100, got {}.", value);
        }

        Guess { value }
    }

    pub fn value(&self) -> i32 {
        self.value
    }
}
```

我们实现了一个借用了 self 的方法 value，它没有任何其他参数并返回一个 i32。这类方法有时被称为 *getter*，因为它的目的就是返回对应字段的数据。这样的公有方法是必要的，因为 Guess 结构体的 value 字段是私有的。私有的字段 value 是很重要的，这样使用 Guess 结构体的代码将不允许直接设置 value 的值：调用者 **必须** 使用 Guess::new 方法来创建一个 Guess 的实例，这就确保了不会存在一个 value 没有通过 Guess::new 函数的条件检查的 Guess。

于是，一个接收（或返回） 1 到 100 之间数字的函数就可以声明为接收（或返回） Guess的实例，而不是 i32，同时其函数体中也无需进行任何额外的检查。

## 在fn main中使用Result

一个典型的 main 函数长这样:

```plain
fn main() {
    println!("Hello World!");
}
```

事实上 main 函数还可以返回一个 Result 类型：如果 main 函数内部发生了错误，那该错误会被返回并且打印出一条错误的 debug 信息。

```plain
use std::num::ParseIntError;

fn main() -> Result<(), ParseIntError> {
    let number_str = "10";
    let number = match number_str.parse::<i32>() {
        Ok(number)  => number,
        Err(e) => return Err(e),
    };
    println!("{}", number);
    Ok(())
}
```

# 十二.泛型

## 函数中定义泛型

寻找vec中的最大值

```plain
fn largest_i32(list: &[i32]) -> i32 {
    let mut largest = list[0];
    for &item in list {
        if item > largest {
            largest = item;
        }
    }
    largest
}

fn largest_char(list: &[char]) -> char {
    let mut largest = list[0];
    for &item in list {
        if item > largest {
            largest = item;
        }
    }
    largest
}

fn main() {
    let number_list = vec![34, 50, 25, 100, 65];
    let result = largest_i32(&number_list);
    println!("The largest number is {}", result);
    let char_list = vec!['y', 'm', 'a', 'q'];
    let result = largest_char(&char_list);
    println!("The largest char is {}", result);
}
```

使用泛型

```plain
fn largest<T>(list: &[T]) -> T {
    let mut largest = list[0];
    for &item in list {
        if item > largest {
            largest = item;
        }
    }
    largest
}

fn main() {
    let number_list = vec![34, 50, 25, 100, 65];
    let result = largest(&number_list);
    println!("The largest number is {}", result);
    let char_list = vec!['y', 'm', 'a', 'q'];
    let result = largest(&char_list);
    println!("The largest char is {}", result);
}
```

运行

```plain
Compiling demo v0.1.0 (C:\Users\cauchy\Desktop\rust\demo)
error[E0369]: binary operation `>` cannot be applied to type `T`
 --> src\main.rs:5:17
  |
5 |         if item > largest {
  |            ---- ^ ------- T
  |            |
  |            T
  |
help: consider restricting type parameter `T`
  |
1 | fn largest<T: std::cmp::PartialOrd>(list: &[T]) -> T {
  |             ++++++++++++++++++++++

For more information about this error, try `rustc --explain E0369`.
error: could not compile `demo` due to previous error
```

简单来说，这个错误表明 largest 的函数体不能适用于 T 的所有可能的类型

因为在函数体需要比较 T 类型的值，不过它只能用于我们知道如何排序的类型。为了开启比较功能，标准库中定义的 std::cmp::PartialOrd trait 可以实现类型的比较功能

## 结构体中定义泛型

文件名: src/main.rs

```plain
struct Point<T> {
    x: T,
    y: T,
}

fn main() {
    let integer = Point { x: 5, y: 10 };
    let float = Point { x: 1.0, y: 4.0 };
}
```

## 枚举中定义泛型

```plain
enum Option<T> {
    Some(T),
    None,
}
```

枚举也可以拥有多个泛型类型。

```plain
enum Result<T, E> {
    Ok(T),
    Err(E),
}
```

## 方法定义中的泛型

文件名: src/main.rs

```plain
struct Point<T> {
    x: T,
    y: T,
}

impl<T> Point<T> {
    fn x(&self) -> &T {
        &self.x
    }
}
impl Point<i32> {
    fn x(&self) -> &i32 {
        &self.x
    }
}

fn main() {
    let p = Point { x: 5, y: 10 };
    println!("p.x = {}", p.x());
}
```

- 把T放在impl关键字后,表示在类型T上实现方法:impl<T> Point<T>
- 只针对具体类型实现方法:impl Point<f32>

```plain
struct Point<T> {
    x: T,
    y: T,
}

impl Point<f32> {
    fn distance_from_origin(&self) -> f32 {
        (self.x.powi(2) + self.y.powi(2)).sqrt()
    }
}

fn main() {
    let p = Point{x: 5.0_f32, y: 10.0_f32};
    println!("{}",p.distance_from_origin())
}
```

- struct里的泛型类型参数可以和方法的泛型类型参数不同

```plain
struct Point<X1, Y1> {
    x: X1,
    y: Y1,
}

impl<X1, Y1> Point<X1, Y1> {
    fn mixup<X2, Y2>(self, other: Point<X2, Y2>) -> Point<X1, Y2> {
        Point {
            x: self.x,
            y: other.y,
        }
    }
}

fn main() {
    let p1 = Point { x: 5, y: 10.4 };
    let p2 = Point { x: "Hello", y: 'c' };

    let p3 = p1.mixup(p2);

    println!("p3.x = {}, p3.y = {}", p3.x, p3.y);
}
```

## const泛型

针对类型实现的泛型，所有的泛型都是为了抽象不同的类型，那有没有针对值的泛型？答案就是 Const 泛型。

```plain
struct ArrayPair<T, const N: usize> {
    left: [T; N],
    right: [T; N],
}

impl<T: Debug, const N: usize> Debug for ArrayPair<T, N> {
    // ...
}
fn foo<const N: usize>() {}

fn bar<T, const M: usize>() {
    foo::<M>(); // ok: 符合第一种
    foo::<2021>(); // ok: 符合第二种
    foo::<{20 * 100 + 20 * 10 + 1}>(); // ok: 符合第三种
    
    foo::<{ M + 1 }>(); // error: 违背第三种，const 表达式中不能有泛型参数 M
    foo::<{ std::mem::size_of::<T>() }>(); // error: 泛型表达式包含了泛型参数 T
    
    let _: [u8; M]; // ok: 符合第一种
    let _: [u8; std::mem::size_of::<T>()]; // error: 泛型表达式包含了泛型参数 T
}

fn main() {}
```

1. 目前，const 泛型参数只能使用以下形式的实参:

- 一个单独的 const 泛型参数
- 一个字面量 (i.e. 整数, 布尔值或字符).
- 一个具体的 const 表达式( 表达式中不能包含任何 泛型参数)

1. const 泛型还能帮我们避免一些运行时检查，提升性能

```plain
pub struct MinSlice<T, const N: usize> {
    pub head: [T; N],
    pub tail: [T],
}

fn main() {
    let slice: &[u8] = b"Hello, world";
    let reference: Option<&u8> = slice.get(6);
    // 我们知道 `.get` 返回的是 `Some(b' ')`
    // 但编译器不知道
    assert!(reference.is_some());

    let slice: &[u8] = b"Hello, world";

    // 当编译构建 MinSlice 时会进行长度检查，也就是在编译期我们就知道它的长度是 12
    // 在运行期，一旦 `unwrap` 成功，在 `MinSlice` 的作用域内，就再无需任何检查    
    let minslice = MinSlice::<u8, 12>::from_slice(slice).unwrap();
    let value: u8 = minslice.head[6];
    assert_eq!(value, b' ')
}
```

`

<T, const N: usize> 是结构体类型的一部分，和数组类型一样，这意味着长度不同会导致类型不同： Array<i32, 3> 和 Array<i32, 4> 是不同的类型

```plain
#[allow(unused)]

struct Array<T, const N: usize> {
    data : [T; N]
}

fn main() {
    let arrays = [
        Array{
            data: [1, 2, 3],
        },
        Array {
            data: [1, 2, 3],
        },
        Array {
            data: [4,5,6]
        }
    ];
}
```

填空

```plain
// 填空
fn print_array<__>(__) {
    println!("{:?}", arr);
}
fn main() {
    let arr = [1, 2, 3];
    print_array(arr);

    let arr = ["hello", "world"];
    print_array(arr);
}
fn print_array<T: std::fmt::Debug, const N: usize>(arr: [T; N]) {
    println!("{:?}", arr);
}
fn main() {
    let arr = [1, 2, 3];
    print_array(arr);

    let arr = ["hello", "world"];
    print_array(arr);
}
```

有时我们希望能**限制一个变量占用内存的大小**，例如在嵌入式环境中，此时 const 泛型参数的第三种形式 const 表达式 就非常适合.

```plain
#![allow(incomplete_features)]
#![feature(generic_const_exprs)]

fn check_size<T>(val: T)
where
    Assert<{ core::mem::size_of::<T>() < 768 }>: IsTrue,
{
    //...
}

// fix the errors in main
fn main() {
    check_size([0u8; 767]); 
    check_size([0i32; 191]);
    check_size(["hello你好"; 47]); // &str is a string reference, containing a pointer and string length in it, so it takes two word long, in x86-64, 1 word = 8 bytes
    check_size([(); 31].map(|_| "hello你好".to_string()));  // String is a smart pointer struct, it has three fields: pointer, length and capacity, each takes 8 bytes
    check_size(['中'; 191]); // A char takes 4 bytes in Rust
}



pub enum Assert<const CHECK: bool> {}

pub trait IsTrue {}

impl IsTrue for Assert<true> {}
```

## 泛型代码的性能

Rust 实现了泛型，使得使用泛型类型参数的代码相比使用具体类型并没有任何速度上的损失。

Rust 通过在编译时进行泛型代码的 **单态化**（*monomorphization*）来保证效率。单态化是一个通过填充编译时使用的具体类型，将通用代码转换为特定代码的过程。

```plain
//fn main(){
//    let integer = Some(5);
//    let float = Some(5.0);
//}
enum Option_i32 {
    Some(i32),
    None,
}

enum Option_f64 {
    Some(f64),
    None,
}

fn main() {
    let integer = Option_i32::Some(5);
    let float = Option_f64::Some(5.0);
}
```

在编译时，rust会将Option泛型展开为Option<i32>和Option<i64>类型

# 十三.Trait：定义共同行为

trait告诉Rust编译器，某种类型具有哪些并且可以与其他类型共享的功能

## 定义一个trait

把方法签名放在一起，来定义实现某种目的所必需的一组行为

- 关键字：trait
- 只有方法签名，没有具体实现

文件名: src/lib.rs

```plain
pub trait Summary {
    fn summarize(&self) -> String;
    fn summarize1(&self) -> String;
}
```

## 在类型上实现trait

与为类型实现方法类似

不同之处：impl Xxxx for Tweet{....}

文件: src/lib.rs

```plain
pub struct NewsArticle {
    pub headline: String,
    pub location: String,
    pub author: String,
    pub content: String,
}

impl Summary for NewsArticle {
    fn summarize(&self) -> String {
        format!("{}, by {} ({})", self.headline, self.author, self.location)
    }
}

pub struct Tweet {
    pub username: String,
    pub content: String,
    pub reply: bool,
    pub retweet: bool,
}

impl Summary for Tweet {
    fn summarize(&self) -> String {
        format!("{}: {}", self.username, self.content)
    }
}
```

文件src/main.rs

```plain
use demo::{Summary,Tweet};
fn main(){
    let tweet = Tweet{
        username: String::from("horse_ebook"),
        content: String::from("of course,sa you probably..."),
        reply:false,
        retweet:false,
    };
    println!("1 new tweet:{}",tweet.summary());
}
```

demo就是Cargo.toml文件中的[package]项的name

## 实现trait的约束

可以在某个类型上实现某个trait的前提条件是:

- 整个类型 **或** 这个trait是在本地crate里定义的

**无法为外部类型实现外部trait**

这个限制是被称为 **相干性**（*coherence*） 的程序属性的一部分，或者更具体的说是 **孤儿规则**（*orphan rule*），其得名于不存在父类型。这条规则确保了其他人编写的代码不会破坏你代码，反之亦然。没有这条规则的话，两个 crate 可以分别对相同类型实现相同的 trait，而 Rust 将无从得知应该使用哪一个实现。

## 默认实现

文件名: src/lib.rs

```plain
pub trait Summary {
    fn summarize(&self) -> String {
        String::from("(Read more...)")
    }
}
pub struct NewsArticle {
    pub headline: String,
    pub location: String,
    pub author: String,
    pub content: String,
}

impl Summary for NewsArticle {
    //fn summarize(&self) -> String {
    //    format!("{}, by {} ({})", self.headline, self.author, self.location)
    //}
}

pub struct Tweet {
    pub username: String,
    pub content: String,
    pub reply: bool,
    pub retweet: bool,
}

impl Summary for Tweet {
    //默认实现的重写的实现
    fn summarize(&self) -> String {
        format!("{}: {}", self.username, self.content)
    }
}
```

**默认实现允许调用相同 trait 中的其他方法**，**哪怕这些方法没有默认实现**

```plain
pub trait Summary {
    fn summarize_author(&self) -> String;

    fn summarize(&self) -> String {
        format!("(Read more from {}...)", self.summarize_author())
    }
}
```

注意:无法从方法的重写实现里面调用默认的实现

## trait作为参数

### impl triat语法：

适用于简单情况,是trait bound的语法糖

```plain
pub fn notify(item: &impl Summary) {
    println!("Breaking news! {}", item.summarize());
}
```

### trait bound语法：

适用于复杂情况

```plain
pub fn notify<T: Summary>(item: &T) {
    println!("Breaking news! {}", item.summarize());
}
```

比较

pub fn notify(item1: &impl Summary, item2: &impl Summary) {

这适用于 item1 和 item2 允许是不同类型的情况（只要它们都实现了 Summary）。不过如果你希望强制它们都是相同类型呢？这只有在使用 trait bound 时才有可能：

pub fn notify<T: Summary>(item1: &T, item2: &T) {

### 使用+指定多个traint bound

pub fn notify(item: &(impl Summary + Display)) {pub fn notify<T: Summary + Display>(item: &T) {

### 使用where简化trait bound

fn some_function<T: Display + Clone, U: Clone + Debug>(t: &T, u: &U) -> i32 {

使用where从句

```plain
fn some_function<T, U>(t: &T, u: &U) -> i32
    where T: Display + Clone,
          U: Clone + Debug
{
```

## 返回实现了trait的类型

也可以在返回值中使用 impl Trait 语法，来返回实现了某个 trait 的类型：

```plain
fn returns_summarizable() -> impl Summary {
    Tweet {
        username: String::from("horse_ebooks"),
        content: String::from(
            "of course, as you probably already know, people",
        ),
        reply: false,
        retweet: false,
    }
}
```

不过这只适用于**返回单一类型的情况**。例如，这段代码的返回值类型指定为返回 impl Summary，但是返回了 NewsArticle 或 Tweet 就行不通：

```plain
fn returns_summarizable(switch: bool) -> impl Summary {
    if switch {
        NewsArticle {
            headline: String::from(
                "Penguins win the Stanley Cup Championship!",
            ),
            location: String::from("Pittsburgh, PA, USA"),
            author: String::from("Iceburgh"),
            content: String::from(
                "The Pittsburgh Penguins once again are the best \
                 hockey team in the NHL.",
            ),
        }
    } else {
        Tweet {
            username: String::from("horse_ebooks"),
            content: String::from(
                "of course, as you probably already know, people",
            ),
            reply: false,
            retweet: false,
        }
    }
}
```

这里尝试返回 NewsArticle 或 Tweet。这不能编译，因为 impl Trait 工作方式的限制。

可以使用dyn trait对象

```plain
struct Sheep {}
struct Cow {}

trait Animal {
    fn noise(&self) -> String;
}

impl Animal for Sheep {
    fn noise(&self) -> String {
        "baaaaah!".to_string()
    }
}

impl Animal for Cow {
    fn noise(&self) -> String {
        "moooooo!".to_string()
    }
}

// 返回一个类型，该类型实现了 Animal 特征，但是我们并不能在编译期获知具体返回了哪个类型
// 修复这里的错误，你可以使用虚假的随机，也可以使用特征对象
fn random_animal(random_number: f64) -> Box<dyn Animal> {
    if random_number < 0.5 {
        Box::new(Sheep {})
    } else {
        Box::new(Cow {})
    }
}

fn main() {
    let random_number = 0.234;
    let animal = random_animal(random_number);
    println!("You've randomly chosen an animal, and it says {}", animal.noise());
}
```

## 特征对象,在数组中使用特征对象

```plain
trait Bird {
    fn quack(&self);
}

struct Duck;
impl Duck {
    fn fly(&self) {
        println!("Look, the duck is flying")
    }
}
struct Swan;
impl Swan {
    fn fly(&self) {
        println!("Look, the duck.. oh sorry, the swan is flying")
    }
}

impl Bird for Duck {
    fn quack(&self) {
        println!("{}", "duck duck");
    }
}

impl Bird for Swan {
    fn quack(&self) {
        println!("{}", "swan swan");
    }
}

fn main() {
    // 填空
    let birds :[Box<dyn Bird>;2]=[Box::new(Duck{}),Box::new(Swan{})];

    for bird in birds {
        bird.quack();
        // 当 duck 和 swan 变成 bird 后，它们都忘了如何翱翔于天际，只记得该怎么叫唤了。。
        // 因此，以下代码会报错
        // bird.fly();
    }
}
```

## &dyn 和Box<dyn>

```plain
trait Draw {
    fn draw(&self) -> String;
}

impl Draw for u8 {
    fn draw(&self) -> String {
        format!("u8: {}", *self)
    }
}

impl Draw for f64 {
    fn draw(&self) -> String {
        format!("f64: {}", *self)
    }
}

fn main() {
    let x = 1.1f64;
    let y = 8u8;

    // draw x
    draw_with_box(Box::new(x));

    // draw y
    draw_with_ref(&y);
}

fn draw_with_box(x: Box<dyn Draw>) {
    x.draw();
}

fn draw_with_ref(x: &dyn Draw) {
    x.draw();
}
```

## 静态分发和动态分发Static and Dynamic dispatch

```plain
trait Foo {
    fn method(&self) -> String;
}

impl Foo for u8 {
    fn method(&self) -> String { format!("u8: {}", *self) }
}

impl Foo for String {
    fn method(&self) -> String { format!("string: {}", *self) }
}

// implement below with generics
fn static_dispatch<T: Foo>(x: T) {
    x.method();
}

// implement below with trait objects
fn dynamic_dispatch(x: &dyn Foo) {
    x.method();
}

fn main() {
    let x = 5u8;
    let y = "Hello".to_string();

    static_dispatch(x);
    dynamic_dispatch(&y);

    println!("Success!")
}
```

## 使用 trait bounds 来修复 largest 函数

文件名: src/main.rs

```plain
fn largest<T: PartialOrd + Copy>(list: &[T]) -> T {
    let mut largest = list[0];

    for &item in list {
        if item > largest {
            largest = item;
        }
    }

    largest
}

fn main() {
    let number_list = vec![34, 50, 25, 100, 65];

    let result = largest(&number_list);
    println!("The largest number is {}", result);

    let char_list = vec!['y', 'm', 'a', 'q'];

    let result = largest(&char_list);
    println!("The largest char is {}", result);
}
```

如果并不希望限制 largest 函数只能用于实现了 Copy trait 的类型，我们可以在 T 的 trait bounds 中指定 Clone 而不是 Copy。并克隆 slice 的每一个值使得 largest 函数拥有其所有权。使用 clone 函数意味着对于类似 String 这样拥有堆上数据的类型，会潜在的分配更多堆上空间，而堆分配在涉及大量数据时可能会相当缓慢。

```plain
fn largest<T: PartialOrd + Clone>(list: &[T]) -> T {
    let mut largest = list[0].clone();

    for item in list {
        if item > &largest {
            largest = item.clone();
        }
    }

    largest
}

fn main() {
    let str_list = vec![String::from("hello"),String::from("world")];
    let result = largest(&str_list)
    println!("The largest word is {}", result);
}
```

或者largest直接返回一个引用

```plain
fn largest<T: PartialOrd + Clone>(list: &[T]) -> &T {
    let mut largest = &list[0];

    for item in list {
        if item > &largest {
            largest = item;
        }
    }

    largest
}

fn main() {
    let str_list = vec![String::from("hello"),String::from("world")];
    let result = largest(&str_list)
    println!("The largest word is {}", result);
}
```

## 使用 trait bound 有条件地实现方法

```plain
use std::fmt::Display;

struct Pair<T> {
    x: T,
    y: T,
}

impl<T> Pair<T> {
    fn new(x: T, y: T) -> Self {
        Self { x, y }
    }
}

impl<T: Display + PartialOrd> Pair<T> {
    fn cmp_display(&self) {
        if self.x >= self.y {
            println!("The largest member is x = {}", self.x);
        } else {
            println!("The largest member is y = {}", self.y);
        }
    }
}
```

为满足trait bound的所有类型上实现trait叫做覆盖实现(blanket implementations)

例如：

```plain
impl<T: Display> ToString for T {
    // --snip--
}
```

因为标准库有了这些 blanket implementation，我们可以对任何实现了 Display trait 的类型调用由 ToString 定义的 to_string 方法。例如，可以将整型转换为对应的 String 值，因为整型实现了 Display：

let s = 3.to_string();

## Derive派生

我们可以使用 #[derive] 属性来派生一些特征，对于这些特征编译器会自动进行默认实现，对于日常代码开发而言，这是非常方便的，例如大家经常用到的 Debug 特征，就是直接通过派生来获取默认实现，而无需我们手动去完成这个工作。

```plain
// `Centimeters`, 一个元组结构体，可以被比较大小
#[derive(PartialEq, PartialOrd)]
struct Centimeters(f64);

// `Inches`, 一个元组结构体可以被打印
#[derive(Debug)]
struct Inches(i32);

impl Inches {
    fn to_centimeters(&self) -> Centimeters {
        let &Inches(inches) = self;

        Centimeters(inches as f64 * 2.54)
    }
}

// 添加一些属性让代码工作
// 不要修改其它代码！
#[derive(Debug,PartialEq,PartialOrd)]
struct Seconds(i32);

fn main() {
    let _one_second = Seconds(1);

    println!("One second looks like: {:?}", _one_second);
    let _this_is_true = _one_second == _one_second;
    let _this_is_true = _one_second > _one_second;

    let foot = Inches(12);

    println!("One foot equals {:?}", foot);

    let meter = Centimeters(100.0);

    let cmp =
        if foot.to_centimeters() < meter {
            "smaller"
        } else {
            "bigger"
        };

    println!("One foot is {} than one meter.", cmp);
}
```

# 十四.生命周期

- Rust的每个引用都有自己的生命周期
- 生命周期：引用保持有效的作用域
- 大多数情况下：生命周期是隐式的，可被推断的
- 当引用的生命周期可能以不同的方式互相关联时：手动标注生命周期

生命周期存在的目标是:**避免悬空引用**

尝试使用离开作用域的值的引用

```plain
{
        let r;

        {
            let x = 5;
            r = &x;
        }

        println!("r: {}", r);
    }
```

## 借用检查器

Rust 编译器有一个 **借用检查器**（*borrow checker*），它比较作用域来确保所有的借用都是有效的。

```plain
{
        let r;                // ---------+-- 'a
                              //          |
        {                     //          |
            let x = 5;        // -+-- 'b  |
            r = &x;           //  |       |
        }                     // -+       |
                              //          |
        println!("r: {}", r); //          |
    }
```

这里将 r 的生命周期标记为 'a 并将 x 的生命周期标记为 'b。如你所见，内部的 'b 块要比外部的生命周期 'a 小得多。在编译时，Rust 比较这两个生命周期的大小，并发现 r 拥有生命周期 'a，不过它引用了一个拥有生命周期 'b 的对象。程序被拒绝编译，因为生命周期 'b 比生命周期 'a 要小：被引用的对象比它的引用者存在的时间更短。

## 函数中的泛型生命周期

```plain
fn longest(x: &str, y: &str) -> &str {
    if x.len() > y.len() {
        x
    } else {
        y
    }
}
fn main() {
    let string1 = String::from("abcd");
    let string2 = "xyz";

    let result = longest(string1.as_str(), string2);
    println!("The longest string is {}", result);
}
```

执行

```plain
error[E0106]: missing lifetime specifier
 --> src\main.rs:1:33
  |
1 | fn longest(x: &str, y: &str) -> &str {
  |               ----     ----     ^ expected named lifetime parameter
  |
  = help: this function's return type contains a borrowed value, but the signature does not say whether it is borrowed from `x` or `y`
help: consider introducing a named lifetime parameter
  |
1 | fn longest<'a>(x: &'a str, y: &'a str) -> &'a str {
  |           ++++     ++          ++          ++

For more information about this error, try `rustc --explain E0106`.
error: could not compile `demo` due to previous error
```

标识生命周期

```plain
fn longest<'a>(x: &'a str, y: &'a str) -> &'a str {
    if x.len() > y.len() {
        x
    } else {
        y
    }
}
fn main() {
    let string1 = String::from("abcd");
    let string2 = "xyz";

    let result = longest(string1.as_str(), string2);
    println!("The longest string is {}", result);
}
```

## 生命周期标注语法

- 生命周期的标注不会改变引用的生命周期的长度
- 当制定了泛型生命周期参数，函数可以接受带有任何生命周期的引用
- 生命周期的标注：**描述了多个引用的生命周期的关系，但不影响生命周期**
- 生命周期的参数名:

以 ' 开头，通常全小写且非常短，很多人使用 'a

- 生命周期标注的位置

**在引用的&符号后**，使用空格将标注和引用类型分开

```plain
&i32        // 引用
&'a i32     // 带有显式生命周期的引用
&'a mut i32 // 带有显式生命周期的可变引用
```

泛型生命周期参数声明在:**函数名和参数列表之间的<>里**

fn longest<'a>(x: &'a str, y: &'a str) -> &'a str {

```plain
fn longest<'a>(x: &'a str, y: &'a str) -> &'a str {
    if x.len() > y.len() {
        x
    } else {
        y
    }
}
```

它的实际含义是 longest 函数返回的引用的生命周期与传入该函数的引用的生命周期的较小者一致。

记住通过在函数签名中指定生命周期参数时，我们并没有改变任何传入值或返回值的生命周期，而是指出任何不满足这个约束条件的值都将被借用检查器拒绝。注意 longest 函数并不需要知道 x 和 y 具体会存在多久，而只需要知道有某个可以被 'a 替代的作用域将会满足这个签名。

当具体的引用被传递给 longest 时，被 'a 所替代的具体生命周期是 x 的作用域与 y 的作用域**相重叠的那一部分**。换一种说法就是泛型生命周期 'a 的具体生命周期等同于 x 和 y 的生命周期中**较小的那一个**。因为我们用相同的生命周期参数 'a 标注了返回的引用值，所以返回的引用值就能保证在 x 和 y 中较短的那个生命周期结束之前保持有效。

使用

```plain
fn main() {
    let string1 = String::from("long string is long");

    {
        let string2 = String::from("xyz");
        let result = longest(string1.as_str(), string2.as_str());
        println!("The longest string is {}", result);
    }
}
```

在这个例子中，string1 直到外部作用域结束都是有效的，string2 则在内部作用域中是有效的，而 result 则引用了一些直到内部作用域结束都是有效的值。借用检查器认可这些代码；它能够编译和运行，并打印出 The longest string is long string is long。

修改

```plain
fn main() {
    let string1 = String::from("long string is long");
    let result;
    {
        let string2 = String::from("xyz");
        result = longest(string1.as_str(), string2.as_str());
    }
    println!("The longest string is {}", result);
}
```

程序运行出错

错误表明为了保证 println! 中的 result 是有效的，string2 需要直到外部作用域结束都是有效的。Rust 知道这些是因为（longest）函数的参数和返回值都使用了相同的生命周期参数 'a。

## 深入理解声明周期

- **指定生命周期参数的正确方式依赖函数实现的具体功能**

如果将 longest 函数的实现修改为总是返回第一个参数而不是最长的字符串 slice，就不需要为参数 y 指定一个生命周期。如下代码将能够编译：

文件名: src/main.rs

```plain
fn longest<'a>(x: &'a str, y: &str) -> &'a str {
    x
}
```

- **当从函数返回一个引用，返回值的生命周期参数需要与一个参数的生命周期参数相匹配。**

如果返回的引用 **没有** 指向任何一个参数，那么唯一的可能就是它指向一个函数内部创建的值，它将会是一个悬垂引用，因为它将会在函数结束时离开作用域。

```plain
fn longest<'a>(x: &str, y: &str) -> &'a str {
    let result = String::from("really long string");
    result.as_str()
}
```

编译报错

综上，生命周期语法是用于**将函数的多个参数与其返回值的生命周期进行关联的**。一旦他们形成了某种关联，Rust 就有了足够的信息来允许内存安全的操作并阻止会产生悬垂指针亦或是违反内存安全的行为。

## 结构体定义中的生命周期注解

struct 里可包括：

- 自持有类型
- 引用:需要在每个引用上添加声明周期标注

文件名: src/main.rs

```plain
struct ImportantExcerpt<'a> {
    part: &'a str,
}

fn main() {
    let novel = String::from("Call me Ishmael. Some years ago...");
    let first_sentence = novel.split('.').next().expect("Could not find a '.'");
    let i = ImportantExcerpt {
        part: first_sentence,
    };
}
```

## 生命周期的省略

**Lifetime Elision**

在Rust引用分析中所编入的模式称为**生命周期省略规则**（*lifetime elision rules*）

这并不是需要程序员遵守的规则；这些规则是一系列特定的场景，此时编译器会考虑，如果代码符合这些场景，就无需明确指定生命周期。

函数或方法的参数的生命周期被称为 **输入生命周期**（*input lifetimes*），而返回值的生命周期被称为 **输出生命周期**（*output lifetimes*）。

编译器采用**三条规则**来判断引用何时不需要明确的注解。第一条规则适用于输入生命周期，后两条规则适用于输出生命周期。如果编译器检查完这三条规则后仍然存在没有计算出生命周期的引用，编译器将会停止并生成错误。这些规则**适用于** **fn** **定义，以及** **impl** **块**。

- **规则1**：每个引用类型的参数都有自己的声明周期
- **规则2**：如果只有1个输入生命周期参数，那么该生命周期被赋给所有的输出生命周期参数
- **规则3**：如果有多个输入生命周期参数，但其中一个是&self或&mut self(是方法)，那么self的生命周期就会被赋给所有的输出生命周期参数

例子:

假设我们自己就是编译器。

fn first_word(s: &str) -> &str {

接着编译器应用第一条规则，也就是**每个引用参数都有其自己的生命周期**。

fn first_word<'a>(s: &'a str) -> &str {

对于第二条规则，因为这里正好只有一个输入生命周期参数所以是适用的。第二条规则表明输入参数的生命周期将被赋予输出生命周期参数，所以现在签名看起来像这样：

fn first_word<'a>(s: &'a str) -> &'a str {

现在这个函数签名中的所有引用都有了生命周期，如此编译器可以继续它的分析而无须程序员标记这个函数签名中的生命周期。

fn longest(x:&str,y:&str)->&str{

应用第一条规则

fn longest<'a,'b>(x:&'a str,y:&'b str)->&str{

第二条规则不适用，第三条不适用，所以编译器报错

## 方法定义中的生命周期标注

当为带有生命周期的结构体实现方法时，其语法依然类似泛型类型参数的语法。

在哪里声明生命周期参数，依赖于：

- 生命周期参数是否同结构体字段或方法参数和返回值相关。

struct字段的生命周期名:

- 在impl后声明
- 在struct名后使用
- 这些生命周期是struct类型的一部分

impl块内的方法签名中:

- 引用必须绑定于struct字段引用的声明周期，或者引用是独立的也可以
- 生命周期省略规则经常使得方法中的生命周期标注不是必须的

```plain
struct ImportantExcerpt<'a>{
    part: &'a str,
}

impl<'a> ImportantExcerpt<'a> {
    fn level(&self) -> i32 {
        3
    }
    //这里是一个适用于第三条生命周期省略规则的例子
    fn announce_and_return_part(&self, announcement: &str) -> &str {
        println!("Attention please: {}", announcement);
        self.part
    }
}

fn main(){
    let novel = String::from("Call me Ishmael.Some year ago...");
    let first_sentence = novel.split('.').next().expect("Could not found a '.'");
    let i = ImportantExcerpt{
        part: first_sentence,
    };
}
```

announce_and_return_part这里有两个输入生命周期，所以 Rust 应用第一条生命周期省略规则并给予 &self 和 announcement 他们各自的生命周期。接着，因为其中一个参数是 &self，返回值类型被赋予了 &self 的生命周期，这样所有的生命周期都被计算出来了。

## 静态生命周期

'static，其生命周期**能够**存活于整个程序期间。

**所有的字符串字面值都拥有** **'static** **生命周期**

let s: &'static str = "I have a static lifetime.";

这个字符串的文本被直接储存在程序的二进制文件中而这个文件总是可用的。因此所有的字符串字面值都是 'static 的。

## 结合泛型类型参数、trait bounds 和生命周期

```plain
use std::fmt::Display;

fn longest_with_an_announcement<'a, T>(
    x: &'a str,
    y: &'a str,
    ann: T,
) -> &'a str
where
    T: Display,
{
    println!("Announcement! {}", ann);
    if x.len() > y.len() {
        x
    } else {
        y
    }
}
```

# 十五.编写自动化测试

Rust 中的测试函数是用来验证非测试代码是否是按照期望的方式运行的。测试函数体通常执行如下三种操作：

1. 设置任何所需的数据或状态(Arrange)
2. 运行需要测试的代码(Act)
3. 断言（Assert）其结果是我们所期望的

## 测试函数剖析

### 编写测试函数

Rust 中的测试就是一个带有 test 属性注解的函数。属性（attribute）是关于 Rust 代码片段的元数据

为了将一个函数变成测试函数，需要在 fn 行之前加上 **#[test]**

### 运行测试

- 使用cargo test命令运行所有测试函数

Rust Hui构建一个Test Runner的可执行文件，它会运行标注了test的函数，并报告其运行是否成功

- 当使用cargo创建library项目时，会生成一个test module,里面有一个test函数

可以添加任意数量的test module或函数

cargo new 项目名 --lib

src/lib.rs

```plain
pub fn add(left: usize, right: usize) -> usize {
    left + right
}

#[cfg(test)]
mod tests {
    use super::*;

    #[test]
    fn it_works() {
        let result = add(2, 2);
        assert_eq!(result, 4);
    }
}
```

cargo run test

### 测试失败

- 测试函数panic就表示失败
- 每个测试运行在一个新线程
- 当主线程看见某个测试线程挂掉了，那个测试标记为失败了

## 断言（Assert）

### 使用assert!宏检查测试结果

assert!宏，来自标准库，用来确定某个状态是否为true

- true，测试通过
- false, 调用panic！，测试失败

```plain
#[derive(Debug)]
struct Rectangle {
    width: u32,
    height: u32,
}

impl Rectangle {
    fn can_hold(&self, other: &Rectangle) -> bool {
        self.width > other.width && self.height > other.height
    }
}

#[cfg(test)]
mod tests {
    use super::*;

    #[test]
    fn larger_can_hold_smaller() {
        let larger = Rectangle {
            width: 8,
            height: 7,
        };
        let smaller = Rectangle {
            width: 5,
            height: 1,
        };

        assert!(larger.can_hold(&smaller));
    }
}
```

### 使用 assert_eq! 和 assert_ne! 宏来测试相等

- 都来自于标准库
- 判断两个参数是否相等或不等
- 实际上，它们使用的就是==和！=运算符的assert!
- 断言失败，会自动打印出两个参数的值

使用debug格式打印参数：要求参数实现了PartialEq和Debug Traits（所有基本类型和标准库大部分类型都实现了）

```plain
pub fn add_two(a: i32) -> i32 {
    a + 2
}

#[cfg(test)]
mod tests {
    use super::*;

    #[test]
    fn it_adds_two() {
        assert_eq!(4, add_two(2));
    }
}
```

### 自定义错误信息

可以向 assert!、assert_eq! 和 assert_ne! 宏传递一个可选的失败信息参数，可以在测试失败时将自定义失败信息一同打印出来。

- assert! 宏的第一个参数必填，自定义消息作为第二个参数
- assert_eq!和assert_ne! 前两个参数必填，自定义消息作为第3个参数
- 自定义消息参数会被传递给format!宏，可以使用{}占位符

```plain
pub fn greeting(name: &str) -> String {
    format!("Hello {}!", name)
}

#[cfg(test)]
mod tests {
    use super::*;

    #[test]
    fn greeting_contains_name() {
        let result = greeting("Carol");
        assert!(result.contains("Carol"));
    }
}
```

自定义错误信息

```plain
#[test]
    fn greeting_contains_name() {
        let result = greeting("Carol");
        assert!(
            result.contains("Carol"),
            "Greeting did not contain name, value was `{}`",
            result
        );
    }
```

## 验证错误处理的情况

可验证代码在特定情况下是否发生了panic

should_panic属性(attribute):

- 函数panic：测试通过
- 函数没有panic：测试失败

```plain
pub struct Guess {
    value: i32,
}

impl Guess {
    pub fn new(value: i32) -> Guess {
        if value < 1 || value > 100 {
            panic!("Guess value must be between 1 and 100, got {}.", value);
        }

        Guess { value }
    }
}

#[cfg(test)]
mod tests {
    use super::*;

    #[test]
    #[should_panic]
    fn greater_than_100() {
        Guess::new(200);
    }
}
```

### 让should_panic更精确

可以给 should_panic 属性增加一个可选的 expected 参数。测试工具会确保错误信息中包含其提供的文本

```plain
// --snip--
impl Guess {
    pub fn new(value: i32) -> Guess {
        if value < 1 {
            panic!(
                "Guess value must be greater than or equal to 1, got {}.",
                value
            );
        } else if value > 100 {
            panic!(
                "Guess value must be less than or equal to 100, got {}.",
                value
            );
        }

        Guess { value }
    }
}

#[cfg(test)]
mod tests {
    use super::*;

    #[test]
    #[should_panic(expected = "Guess value must be less than or equal to 100")]
    fn greater_than_100() {
        Guess::new(200);
    }
}
```

## 在测试中使用Result<T,E>

无需panic，可以使用Result<T,E>作为返回类型编写测试

- 返回Ok:测试通过
- 返回Err:测试失败

```plain
#[cfg(test)]
mod tests {
    #[test]
    fn it_works() -> Result<(), String> {
        if 2 + 2 == 4 {
            Ok(())
        } else {
            Err(String::from("two plus two does not equal four"))
        }
    }
}
```

注意：不能对这些使用 Result<T, E> 的测试使用 #[should_panic] 注解。

因为运行失败时会返回Err而不会发生panic

## 控制测试运行

改变cargo test的行为:添加命令行参数

默认行为:

- 并行运行
- 所有测试
- 捕获（不显示）所有输出，是读取与测试结果相关的输出更容易

命令行参数:

- 针对cargo test的参数: 紧跟cargo test后 

cargo test --help

- 针对测试可执行程序: 放在--之后

cargo test -- --help

### 并行运行测试

默认使用多个线程并行运行

要确保测试之间：

- 不会相互依赖
- 不依赖于某个共享状态(环境，工作目录，环境变量等)

#### --test-threads 参数

如果你不希望测试并行运行，或者想要更加精确的控制线程的数量，可以传递 --test-threads 参数和希望使用线程的数量给测试二进制文件。例如：

$ cargo test -- --test-threads=1

这里将测试线程设置为 1，告诉程序不要使用任何并行机制。这也会比并行运行花费更多时间，不过在有共享的状态时，测试就不会潜在的相互干扰了。

### 显式函数输出

默认，如果测试通过，Rust的test库会捕获所有打印到标准输出的内容

比如println!:

- 如果测试成功，我们将不会在终端看到 println! 的输出：只会看到说明测试通过的提示行。
- 如果测试失败了，则会看到所有标准输出和其他错误信息。

如果你希望也能看到通过的测试中打印的值，也可以在结尾加上 --show-output 告诉 Rust 显示成功测试的输出。

$ cargo test -- --show-output

### 按名称运行测试的子集

如果没有传递任何参数就运行测试，所有测试都会并行运行：

#### 运行单个测试

可以向 cargo test 传递任意测试的名称来只运行这个测试：

$ cargo test one_hundred

#### 过滤运行多个测试

我们可以指定部分测试的名称，任何名称匹配这个名称的测试会被运行。例如，因为头两个测试的名称包含 add，可以通过 cargo test add 来运行这两个测试：

```plain
$ cargo test add
   Compiling adder v0.1.0 (file:///projects/adder)
    Finished test [unoptimized + debuginfo] target(s) in 0.61s
     Running unittests (target/debug/deps/adder-92948b65e88960b4)

running 2 tests
test tests::add_three_and_two ... ok
test tests::add_two_and_two ... ok

test result: ok. 2 passed; 0 failed; 0 ignored; 0 measured; 1 filtered out; finished in 0.00s
```

这运行了所有名字中带有 add 的测试，也过滤掉了名为 one_hundred 的测试。

#### 忽略某些测试

有时一些特定的测试执行起来是非常耗费时间的，所以在大多数运行 cargo test 的时候希望能排除他们

可以使用 ignore 属性来标记耗时的测试并排除他们，如下所示：

文件名: src/lib.rs

```plain
#[test]
fn it_works() {
    assert_eq!(2 + 2, 4);
}

#[test]
#[ignore]
fn expensive_test() {
    // 需要运行一个小时的代码
}
```

对于想要排除的测试，我们在 #[test] 之后增加了 #[ignore] 行。现在如果运行测试，就会发现 it_works 运行了，而 expensive_test 没有运行：

如果我们只希望运行被忽略的测试，可以使用 cargo test -- --ignored：

$ cargo test -- --ignored

## 测试组织

Rust 社区倾向于根据测试的两个主要分类来考虑问题：

**单元测试**（*unit tests*）与 **集成测试**（*integration tests*）

- 元测试倾向于更小而更集中，在隔离的环境中一次测试一个模块，或者是测试私有接口。
- 而集成测试对于你的库来说则完全是外部的。它们与其他外部代码一样，通过相同的方式使用你的代码，只测试公有接口而且每个测试都有可能会测试多个模块。

### 单元测试

**#[cfg(test)]标注test模块**

- 只有运行cargo test才编译和运行代码
- 运行cargo build则不会
- 集成测试在不同的目录，它不需要#[cfg(test)]标注

**cfg: configuration配置**

告诉Rust下面的条目只有在指定的配置选项下才被包含

#### 测试私有函数

测试社区中一直存在关于是否应该对私有函数直接进行测试的论战，而在其他语言中想要测试私有函数是一件困难的，甚至是不可能的事。不过无论你坚持哪种测试意识形态，**Rust 的私有性规则确实允许你测试私有函数**。

文件名: src/lib.rs

```plain
pub fn add_two(a: i32) -> i32 {
    internal_adder(a, 2)
}

fn internal_adder(a: i32, b: i32) -> i32 {
    a + b
}

#[cfg(test)]
mod tests {
    use super::*;

    #[test]
    fn internal() {
        assert_eq!(4, internal_adder(2, 2));
    }
}
```

示例 11-12：测试私有函数

注意 internal_adder 函数并没有标记为 pub。测试也不过是 Rust 代码，同时 tests 也仅仅是另一个模块。正如 “路径用于引用模块树中的项”部分所说，子模块的项可以使用其上级模块的项。在测试中，我们通过 use super::* 将 test 模块的父模块的所有项引入了作用域，接着测试调用了 internal_adder。

### 集成测试

在Rust里，启程测试完全位于测试库的外部

同其他使用库的代码一样使用库文件，也就是说它们只能调用一部分库中的公有 API 。集成测试的目的是测试库的多个部分能否一起正常工作。一些单独能正确运行的代码单元集成在一起也可能会出现问题，所以**集成测试的覆盖率**也是很重要的。

#### tests目录

- 创建集成测试:tests目录
- tests目录下的每个测试文件都是单独的一个crate

创建一个集成测试。保留示例adder中 *src/lib.rs* 的代码。创建一个 *tests* 目录，新建一个文件 *tests/integration_test.rs*，并输入示例中的代码。

```plain
use adder;

#[test]
fn it_adds_two() {
    assert_eq!(4, adder::add_two(2));
}
```

- 并不需要将 *tests/integration_test.rs* 中的任何代码标注为 #[cfg(test)]。 tests 文件夹在 Cargo 中是一个特殊的文件夹， Cargo 只会在运行 cargo test 时编译这个目录中的文件。
- 需要将被测试库导入

#### 运行指定的集成测试

运行一个特定的集成测试

cargo test 函数名

运行某个测试文件内的所有测试

cargo test --test 文件名

#### 集成测试中的子模块

随着集成测试的增加，你可能希望在 tests 目录增加更多文件以便更好的组织他们，例如根据测试的功能来将测试分组。正如我们之前提到的，每一个 *tests* 目录中的文件都被编译为单独的 crate。

将每个集成测试文件当作其自己的 crate 来对待，这更有助于创建单独的作用域，这种单独的作用域能提供更类似与最终使用者使用 crate 的环境。

例如，如果我们可以创建 一个*tests/common.rs* 文件并创建一个名叫 setup 的函数，我们希望这个函数能被多个测试文件的测试函数调用：

文件名: tests/common.rs

```plain
pub fn setup() {
    // setup code specific to your library's tests would go here
}
```

如果再次运行测试，将会在测试结果中看到一个新的对应 *common.rs* 文件的测试结果部分，即便这个文件并没有包含任何测试函数，也没有任何地方调用了 setup 函数：

```plain
$ cargo test
   Compiling adder v0.1.0 (file:///projects/adder)
    Finished test [unoptimized + debuginfo] target(s) in 0.89s
     Running unittests (target/debug/deps/adder-92948b65e88960b4)

running 1 test
test tests::internal ... ok

test result: ok. 1 passed; 0 failed; 0 ignored; 0 measured; 0 filtered out; finished in 0.00s

     Running tests/common.rs (target/debug/deps/common-92948b65e88960b4)

running 0 tests

test result: ok. 0 passed; 0 failed; 0 ignored; 0 measured; 0 filtered out; finished in 0.00s

     Running tests/integration_test.rs (target/debug/deps/integration_test-92948b65e88960b4)

running 1 test
test it_adds_two ... ok

test result: ok. 1 passed; 0 failed; 0 ignored; 0 measured; 0 filtered out; finished in 0.00s

   Doc-tests adder

running 0 tests

test result: ok. 0 passed; 0 failed; 0 ignored; 0 measured; 0 filtered out; finished in 0.00s
```

我们并不想要common 出现在测试结果中显示 running 0 tests 。我们只是希望其能被其他多个集成测试文件中调用罢了。

为了不让 common 出现在测试输出中，我们将**创建** ***tests/common/mod.rs*** ，**而不是创建** ***tests/common.rs*** 。这是一种 Rust 的命名规范，这样命名**告诉 Rust 不要将** **common** **看作一个集成测试文件**。将 setup 函数代码移动到 *tests/common/mod.rs* 并删除 *tests/common.rs* 文件之后，测试输出中将不会出现这一部分。*tests* 目录中的子目录不会被作为单独的 crate 编译或作为一个测试结果部分出现在测试输出中。

一旦拥有了 *tests/common/mod.rs*，就可以将其作为模块以便在任何集成测试文件中使用。这里是一个 *tests/integration_test.rs* 中调用 setup 函数的 it_adds_two 测试的例子：

文件名: tests/integration_test.rs

```plain
use adder;

mod common;

#[test]
fn it_adds_two() {
    common::setup();
    assert_eq!(4, adder::add_two(2));
}接着在测试函数中就可以调用 `common::setup()` 了。
```

#### 针对binary crate的集成测试

- 如果项目时binary crate 只有含有src/main.rs 没有src/lib.rs：

不能在tests目录下创建集成测试

无法把main.rs的函数导入作用域

- 只有library crate在能暴露函数给其它crate使用
- binary crate意味着独立运行

# 十六.IO项目:构建命令行程序

## 接收命令行参数

```plain
use std::env;

fn main() {
    let args: Vec<String> = env::args().collect();
    println!("{:?}", args);
}
```

注意:

注意 std::env::args 在其任何参数包含无效 Unicode 字符时会 panic。如果你需要接受包含无效 Unicode 字符的参数，使用 std::env::args_os 代替。这个函数返回 OsString 值而不是 String 值。这里出于简单考虑使用了 std::env::args，因为 OsString 值每个平台都不一样而且比 String 值处理起来更为复杂。

## 二进制项目的关注与分离

main 函数负责多个任务的组织问题在许多二进制项目中很常见。所以 Rust 社区开发出一类在 main 函数开始变得庞大时进行二进制程序的关注分离的指导性过程。这些过程有如下步骤：

- 将程序拆分成 *main.rs* 和 *lib.rs* 并将程序的逻辑放入 *lib.rs* 中。
- 当命令行解析逻辑比较小时，可以保留在 *main.rs* 中。
- 当命令行解析开始变得复杂时，也同样将其从 *main.rs* 提取到 *lib.rs* 中。

经过这些过程之后保留在 main 函数中的责任应该被限制为：

- 使用参数值调用命令行解析逻辑
- 设置任何其他的配置
- 调用 *lib.rs* 中的 run 函数
- 如果 run 返回错误，则处理这个错误

这个模式的一切就是为了关注分离：*main.rs* 处理程序运行，而 *lib.rs* 处理所有的真正的任务逻辑。因为不能直接测试 main 函数，这个结构通过将所有的程序逻辑移动到 *lib.rs* 的函数中使得我们可以测试他们。仅仅保留在 *main.rs* 中的代码将足够小以便阅读就可以验证其正确性。

## 测试驱动开发

遵循测试驱动开发（Test Driven Development, TDD）的模式来逐步增加 minigrep 的搜索逻辑。这是一个软件开发技术，它遵循如下步骤：

1. 编写一个失败的测试，并运行它以确保它失败的原因是你所期望的。
2. 编写或修改足够的代码来使新的测试通过。
3. 重构刚刚增加或修改的代码，并确保测试仍然能通过。
4. 从步骤 1 开始重复！

这只是众多编写软件的方法之一，不过 TDD 有助于驱动代码的设计。在编写能使测试通过的代码之前编写测试有助于在开发过程中保持高测试覆盖率。

## 编写minigrep代码

src/lib.rs

```plain
use std::env;
use std::error::Error;
use std::fs;

pub fn run(config: Config) -> Result<(), Box<dyn Error>> {
    //读取文件
    let contents = fs::read_to_string(&config.filename)?;
    // println!("With text:\n{}", contents);
    let results = if config.case_sensitive {
        search(&config.query, &contents)
    } else {
        search_case_insensitive(&config.query, &contents)
    };
    for line in results {
        println!("{}", line);
    }
    Ok(())
}
pub struct Config {
    pub query: String,
    pub filename: String,
    pub case_sensitive: bool,
}
impl Config {
    pub fn new(args: &[String]) -> Result<Config, &'static str> {
        if args.len() < 3 {
            return Err("not enough arguments");
        }
        let query = args[1].clone();
        let filename = args[2].clone();
        // println!("Search for {}", query);
        // println!("In file {}", filename);
        let case_sensitive = env::var("CASE_INSENSITIVE").is_err();
        Ok(Config {
            query,
            filename,
            case_sensitive,
        })
    }
}

pub fn search<'a>(query: &str, contents: &'a str) -> Vec<&'a str> {
    let mut result = Vec::new();
    for line in contents.lines() {
        if line.contains(query) {
            result.push(line);
        }
    }

    result
}
pub fn search_case_insensitive<'a>(query: &str, contents: &'a str) -> Vec<&'a str> {
    let mut result = Vec::new();
    let query = query.to_lowercase();
    for line in contents.lines() {
        if line.to_lowercase().contains(&query) {
            result.push(line);
        }
    }

    result
}

#[cfg(test)]
mod tests {
    use super::*;
    #[test]
    fn case_sensitive() {
        let query = "duct";
        let contents = "\
Rust:
safe,fast,productive.
Pick three.";
        assert_eq!(vec!["safe,fast,productive."], search(query, contents));
    }

    #[test]
    fn case_insensitive() {
        let query = "duct";
        let contents = "\
Rust:
safe,fast,productive.
Pick three.";
        assert_eq!(
            vec!["safe,fast,productive."],
            search_case_insensitive(query, contents)
        );
    }
}
```

src/main.rs

```plain
use std::env;
use std::process;
use minigrep::Config;

fn main() {
    let args: Vec<String> = env::args().collect();
    // println!("{:?}",args);
    let config = Config::new(&args).unwrap_or_else(|err| {
        eprintln!("Problem parsing arguments:{}", err);
        process::exit(1);
    });
    if let Err(e) = minigrep::run(config){
        eprintln!("Application error: {}",e);
        process::exit(1);
    };
}
```

# 十七.函数式语言特性:迭代器和闭包

## 闭包

Rust 的 **闭包**（*closures*）是可以保存在一个变量中或作为参数传递给其他函数的匿名函数

- 匿名函数
- 保存为变量,作为参数
- 可以在一个地方创建闭包，然后在另一个上下文中调用闭包来完成运算
- 可从其定义的作用域捕获值

文件名: src/main.rs

一个用来代替假定计算的函数，它大约会执行两秒钟

```plain
use std::thread;
use std::time::Duration;

fn simulated_expensive_calculation(intensity: u32) -> u32 {
    println!("calculating slowly...");
    thread::sleep(Duration::from_secs(2));
    intensity
}
```

文件名: src/main.rs

程序的业务逻辑，它根据输入并调用 simulated_expensive_calculation 函数来打印出健身计划

```plain
fn generate_workout(intensity: u32, random_number: u32) {
    if intensity < 25 {
        println!(
            "Today, do {} pushups!",
            simulated_expensive_calculation(intensity)
        );
        println!(
            "Next, do {} situps!",
            simulated_expensive_calculation(intensity)
        );
    } else {
        if random_number == 3 {
            println!("Take a break today! Remember to stay hydrated!");
        } else {
            println!(
                "Today, run for {} minutes!",
                simulated_expensive_calculation(intensity)
            );
        }
    }
}
```

文件名: src/main.rs

main 函数包含了用于 generate_workout 函数的模拟用户输入和模拟随机数输入

```plain
fn main() {
    let simulated_user_specified_value = 10;
    let simulated_random_number = 7;

    generate_workout(simulated_user_specified_value, simulated_random_number);
}
```

### 闭包的定义

```plain
let expensive_closure = |num| {
        println!("calculating slowly...");
        thread::sleep(Duration::from_secs(2));
        num
    };
```

- 闭包定义是 expensive_closure 赋值的 = 之后的部分。闭包的定义以一对竖线（|）开始，在竖线中指定闭包的参数；
- 如果有多于一个参数，可以使用逗号分隔，比如 |param1, param2|。
- 参数之后是存放闭包体的大括号 —— 如果闭包体只有一行则大括号是可以省略的。大括号之后闭包的结尾，需要用于 let 语句的分号。因为闭包体的最后一行没有分号（正如函数体一样），所以闭包体（num）最后一行的返回值作为调用闭包时的返回值 。

注意:

这个 let 语句意味着 expensive_closure 包含一个匿名函数的 **定义**，不是调用匿名函数的 **返回值**。

**重构代码**

文件名: src/main.rs

```plain
fn generate_workout(intensity: u32, random_number: u32) {
    let expensive_closure = |num| {
        println!("calculating slowly...");
        thread::sleep(Duration::from_secs(2));
        num
    };

    if intensity < 25 {
        println!("Today, do {} pushups!", expensive_closure(intensity));
        println!("Next, do {} situps!", expensive_closure(intensity));
    } else {
        if random_number == 3 {
            println!("Take a break today! Remember to stay hydrated!");
        } else {
            println!(
                "Today, run for {} minutes!",
                expensive_closure(intensity)
            );
        }
    }
}
```

### 闭包的类型推断

- 闭包不要求标注参数和返回值的类型
- 闭包通常很短小，只在狭小的上下文中工作，编译器通常能推断出类型
- 可以手动添加类型

```plain
let expensive_closure = |num: u32| -> u32 {
        println!("calculating slowly...");
        thread::sleep(Duration::from_secs(2));
        num
    };
```

与函数的比较

```plain
fn  add_one_v1   (x: u32) -> u32 { x + 1 }//函数
let add_one_v2 = |x: u32| -> u32 { x + 1 };//闭包
let add_one_v3 = |x|             { x + 1 };//闭包
let add_one_v4 = |x|               x + 1  ;//闭包
```

src/main.rs

```plain
let example_closure = |x| x;
    let s = example_closure(String::from("hello"));
    let n = example_closure(5);
```

这个闭包在执行第二行代码时，编译器就能确定该闭包的类型是String了，而在执行第三行时就会报错

### 泛型参数闭包

在上面的代码仍然把慢计算闭包调用了比所需更多的次数。解决这个问题的一个方法是在全部代码中的每一个需要多个慢计算闭包结果的地方，可以将结果保存进变量以供复用，这样就可以使用变量而不是再次调用闭包。但是这样就会有很多重复的保存结果变量的地方。

幸运的是，还有另一个可用的方案。可以**创建一个存放闭包和调用闭包结果的结构体**。该结构体只会在需要结果时执行闭包，并会缓存结果值，这样余下的代码就不必再负责保存结果并可以复用该值。你可能见过这种模式被称 ***memoization\*** 或 ***lazy evaluation\****（惰性求值）*。

#### 如何让struct持有闭包

- struct的定义需要知道所有字段的类型，即需要指明闭包的类型
- 每个闭包实例都有自己唯一的匿名类型，即使两个闭包签名完全一样
- 所以需要使用:泛型和Trait Bound

**Fn Trait**

- 由标准库提供
- 所有的闭包都至少实现了以下trait之一: 

​			Fn FnMut FnOnce

注意：**函数也都实现了这三个** **Fn** **trait**。如果不需要捕获环境中的值，则可以使用实现了 Fn trait 的函数而不是闭包。

```plain
struct Cacher<T>
where
    T: Fn(u32) -> u32,
{
    calculation: T,
    value: Option<u32>,
}
impl<T> Cacher<T>
where
    T: Fn(u32) -> u32,
{
    fn new(calculation: T) -> Cacher<T> {
        Cacher {
            calculation,
            value: None,
        }
    }

    fn value(&mut self, arg: u32) -> u32 {
        match self.value {
            Some(v) => v,
            None => {
                let v = (self.calculation)(arg);
                self.value = Some(v);
                v
            }
        }
    }
}
```

结构体 Cacher 有一个泛型 T 的字段 calculation。T 的 trait bound 指定了 T 是一个使用 Fn 的闭包。任何我们希望储存到 Cacher 实例的 calculation 字段的闭包必须有一个 u32 参数（由 Fn 之后的括号的内容指定）并必须返回一个 u32（由 -> 之后的内容）。

字段 value 是 Option<u32> 类型的。在执行闭包之前，value 将是 None。如果使用 Cacher 的代码请求闭包的结果，这时会执行闭包并将结果储存在 value 字段的 Some 成员中。接着如果代码再次请求闭包的结果，这时不再执行闭包，而是会返回存放在 Some 成员中的结果。

重构代码

```plain
fn generate_workout(intensity: u32, random_number: u32) {
    let mut expensive_result = Cacher::new(|num| {
        println!("calculating slowly...");
        thread::sleep(Duration::from_secs(2));
        num
    });

    if intensity < 25 {
        println!("Today, do {} pushups!", expensive_result.value(intensity));
        println!("Next, do {} situps!", expensive_result.value(intensity));
    } else {
        if random_number == 3 {
            println!("Take a break today! Remember to stay hydrated!");
        } else {
            println!(
                "Today, run for {} minutes!",
                expensive_result.value(intensity)
            );
        }
    }
}
```

#### Cacher实现的限制

1. 第一个问题是 Cacher 实例假设对于 value 方法的任何 arg 参数值总是会返回相同的值。

**解决方案**：

可以使用HashMap代替单个值:

​	key: arg参数

​	value: 执行闭包的结果

1. 第二个问题是只能接收一个u32类型的参数和u32类型的返回值

**解决方案:**

引入两个或多个泛型参数

### 闭包会捕获其环境

- 可以捕获其环境并访问其被定义的作用域的变量,而普通函数则不能

```plain
fn main() {
    let x = 4;

    let equal_to_x = |z| z == x;

    let y = 4;

    assert!(equal_to_x(y));
}
```

- 会产生内存开销

#### 闭包从所在环境捕获值的方式

与函数获得参数的三种方式一样:

- 取得所有权: **FnOnce**
- 可变借用: **FnMut**
- 不可变借用: **Fn**
- FnOnce 消费从周围作用域捕获的变量，闭包周围的作用域被称为其 **环境**，*environment*。为了消费捕获到的变量，闭包必须获取其所有权并在定义闭包时将其移动进闭包。其名称的 Once 部分代表了闭包不能多次获取相同变量的所有权的事实，所以它只能被调用一次。
- FnMut 获取可变的借用值所以可以改变其环境
- Fn 从其环境获取不可变的借用值

创建闭包时，通过闭包对环境值的使用，Rust推断出具体使用哪个trait：

- 所有的闭包都实现了FnOnce
- 没有移动捕获变量的实现了FnMut
- 无需可变访问捕获变量的闭包实现了Fn

实际上有一个层级关系，所有实现了Fn的都实现了FnMut，所有实现了FnMut的，都实现了FnOnce

#### move关键字

在参数列表前使用move关键字，可以强制闭包取得它所使用的环境值得所有权

- 当将闭包传递给新线程以移动数据使其归新线程所有时，此技术最为有用

例子

```plain
fn main() {
    let x = vec![1, 2, 3];

    let equal_to_x = move |z| z == x;

    println!("can't use x here: {:?}", x);

    let y = vec![1, 2, 3];

    assert!(equal_to_x(y));
}
```

x 被移动进了闭包，因为闭包使用 move 关键字定义。接着闭包获取了 x 的所有权，同时 main 就不再允许在 println! 语句中使用 x 了。去掉 println! 即可修复问题。

#### 最佳实践

当指定Fn trait bound之一时，首先用Fn,基于闭包体里得情况，如果需要FnOnce或FnMut，编译器会再告诉你

## 迭代器

迭代器模式允许你对一个序列的项进行某些处理。**迭代器**（*iterator*）负责遍历序列中的每一项和决定序列何时结束的逻辑。当使用迭代器时，我们无需重新实现这些逻辑。

在 Rust 中，迭代器是 **惰性的**（*lazy*），这意味着在调用方法使用迭代器之前它都不会有效果。

```plain
let v1 = vec![1, 2, 3];

    let v1_iter = v1.iter();
```

### Iterator trait

- 所有迭代器都实现了这个trait
- 定义于标准库

这个 trait 的定义看起来像这样：

```plain
pub trait Iterator {
    type Item;

    fn next(&mut self) -> Option<Self::Item>;

    // 此处省略了方法的默认实现
}
```

type Item 和 Self::Item，他们定义了 trait 的 **关联类型**（*associated type*）。

这段代码表明实现 Iterator trait 要求同时**定义一个** **Item** **类型**，这个 Item 类型被用作 **next** **方法的返回值类型**。换句话说，Item 类型将是迭代器返回元素的类型。

Iterator trait仅要求实现一个方法：next

next:

- 每次返回迭代中的一项
- 返回结果包裹在Some里
- 迭代结束，返回None

可直接在迭代器上调用next方法

```plain
#[test]
    fn iterator_demonstration() {
        let v1 = vec![1, 2, 3];

        let mut v1_iter = v1.iter();

        assert_eq!(v1_iter.next(), Some(&1));
        assert_eq!(v1_iter.next(), Some(&2));
        assert_eq!(v1_iter.next(), Some(&3));
        assert_eq!(v1_iter.next(), None);
    }
```

- v1_iter 需要是可变的：在迭代器上调用 next 方法改变了迭代器中用来记录序列位置的状态。换句话说，代码 **消费**（consume）了，或使用了迭代器。每一个 next 调用都会从迭代器中消费一个项。
- 使用 for 循环时无需使 v1_iter 可变因为 for 循环会获取 v1_iter 的所有权并在后台使 v1_iter 可变。

#### 几个迭代方法

- iter方法：在不可变引用上创建迭代器（元素的不可变引用）
- into_iter方法：创建的迭代器会获得所有权
- iter_mut方法: 迭代可变的引用

### 消耗迭代器的方法

- 在标准库中，Iterator trait由一些带默认实现的方法
- 其中有一些方法会调用next方法

实现Iterator trait时必须实现next方法的原因之一

- 调用next的叫做“**消耗型适配器**”

因为调用它们会把迭代器耗尽

一个消费适配器的例子是 sum 方法。这个方法获取迭代器的所有权并反复调用 next 来遍历迭代器，因而会消费迭代器。当其遍历每一个项时，它将每一个项加总到一个总和并在迭代完成时返回总和。

文件名: src/lib.rs

```plain
#[test]
    fn iterator_sum() {
        let v1 = vec![1, 2, 3];

        let v1_iter = v1.iter();

        let total: i32 = v1_iter.sum();

        assert_eq!(total, 6);
    }
```

### 产生其它迭代器的方法

Iterator trait 中定义了另一类方法，被称为 **迭代器适配器**（*iterator adaptors*），

- 他们允许将当前迭代器变为不同类型的迭代器。
- 可以链式调用多个迭代器适配器。
- 不过因为所有的迭代器都是惰性的，必须调用一个消费适配器方法以便获取迭代器适配器调用的结果。

文件名: src/main.rs

```plain
let v1: Vec<i32> = vec![1, 2, 3];

    v1.iter().map(|x| x + 1);
```

该 map 方法使用闭包来调用每个元素以生成新的迭代器。 这里的闭包创建了一个新的迭代器，对其中 vector 中的每个元素都被加 1。

不过这些代码会产生一个警告：

 = note: iterators are lazy and do nothing unless consumed

代码实际上并没有做任何事；所指定的闭包从未被调用过。警告提醒了我们为什么：迭代器适配器是惰性的，而这里我们需要消费迭代器。

文件名: src/main.rs

```plain
let v1: Vec<i32> = vec![1, 2, 3];

    let v2: Vec<_> = v1.iter().map(|x| x + 1).collect();

    assert_eq!(v2, vec![2, 3, 4]);
```

第二行代码的下划线实际上是让编译器去推断他的类型

collect方法是一个**消耗型适配器**，把结果收集到一个集合类型中

因为 map 获取一个闭包，可以指定任何希望在遍历的每个元素上执行的操作。这是一个展示如何使用闭包来自定义行为同时又复用 Iterator trait 提供的迭代行为的绝佳例子。

\### 

### 使用闭包捕获环境

filter方法

- 迭代器的 filter 方法获取一个使用迭代器的每一个项并返回布尔值的闭包。
- 如果闭包返回 true，其值将会包含在 filter 提供的新迭代器中。
- 如果闭包返回 false，其值不会包含在结果迭代器中。

文件名: src/lib.rs

```plain
#[derive(PartialEq, Debug)]
struct Shoe {
    size: u32,
    style: String,
}

fn shoes_in_size(shoes: Vec<Shoe>, shoe_size: u32) -> Vec<Shoe> {
    shoes.into_iter().filter(|s| s.size == shoe_size).collect()
    //闭包从环境中捕获了 `shoe_size` 变量并使用其值与每一只鞋的大小作比较
}

#[cfg(test)]
mod tests {
    use super::*;

    #[test]
    fn filters_by_size() {
        let shoes = vec![
            Shoe {
                size: 10,
                style: String::from("sneaker"),
            },
            Shoe {
                size: 13,
                style: String::from("sandal"),
            },
            Shoe {
                size: 10,
                style: String::from("boot"),
            },
        ];

        let in_my_size = shoes_in_size(shoes, 10);

        assert_eq!(
            in_my_size,
            vec![
                Shoe {
                    size: 10,
                    style: String::from("sneaker")
                },
                Shoe {
                    size: 10,
                    style: String::from("boot")
                },
            ]
        );
    }
}
```

shoes_in_my_size 函数获取一个鞋子 vector 的所有权和一个鞋子大小作为参数。它返回一个只包含指定大小鞋子的 vector。

shoes_in_my_size 函数体中调用了 into_iter 来创建一个获取 vector 所有权的迭代器。接着调用 filter 将这个迭代器适配成一个只含有那些闭包返回 true 的元素的新迭代器。

闭包从环境中捕获了 shoe_size 变量并使用其值与每一只鞋的大小作比较，只保留指定大小的鞋子。最终，调用 collect 将迭代器适配器返回的值收集进一个 vector 并返回。

### 创建自定义迭代器

，定义中唯一要求提供的方法就是 next 方法。一旦定义了它，就可以使用所有其他由 Iterator trait 提供的拥有默认实现的方法来创建自定义迭代器了！

```plain
struct Counter {
    count: u32,
}

impl Counter {
    fn new() -> Counter {
        Counter { count: 0 }
    }
}

impl Iterator for Counter {
    type Item = u32;
    //这里将迭代器的关联类型 Item 设置为 u32，意味着迭代器会返回 u32 值集合。

    fn next(&mut self) -> Option<Self::Item> {
        if self.count < 5 {
            self.count += 1;
            Some(self.count)
        } else {
            None
        }
        //如果 count 值小于 6，next 会返回封装在 Some 中的当前值，
        //不过如果 count 大于或等于 6，迭代器会返回 None。
    }
}

  #[test]
    fn calling_next_directly() {
        let mut counter = Counter::new();

        assert_eq!(counter.next(), Some(1));
        assert_eq!(counter.next(), Some(2));
        assert_eq!(counter.next(), Some(3));
        assert_eq!(counter.next(), Some(4));
        assert_eq!(counter.next(), Some(5));
        assert_eq!(counter.next(), None);
    }
```

通过定义 next 方法实现 Iterator trait，我们现在就可以使用任何标准库定义的拥有默认实现的 Iterator trait 方法了，因为他们都使用了 next 方法的功能。

例如，出于某种原因我们希望获取 Counter 实例产生的值，将这些值与另一个 Counter 实例在**省略了第一个值**之后产生的值配对，**将每一对值相乘**，**只保留那些可以被三整除的结果**，**然后将所有保留的结果相加**，这可以如示例 13-23 中的测试这样做：

文件名: src/lib.rs

```plain
#[test]
    fn using_other_iterator_trait_methods() {
        let sum: u32 = Counter::new()
            .zip(Counter::new().skip(1))
            .map(|(a, b)| a * b)
            .filter(|x| x % 3 == 0)
            .sum();
        assert_eq!(18, sum);
    }
```

示例 13-23：使用自定义的 Counter 迭代器的多种方法

注意 zip 只产生四对值；理论上第五对值 (5, None) 从未被产生，因为 zip 在任一输入迭代器返回 None 时也返回 None。

所有这些方法调用都是可能的，因为我们指定了 next 方法如何工作，而标准库则提供了其它调用 next 的方法的默认实现。

### 改进I/O项目

#### 使用迭代器并去掉clone

文件名: src/lib.rs

```plain
impl Config {
    pub fn new(args: &[String]) -> Result<Config, &'static str> {
        if args.len() < 3 {
            return Err("not enough arguments");
        }

        let query = args[1].clone();
        let filename = args[2].clone();

        let case_sensitive = env::var("CASE_INSENSITIVE").is_err();

        Ok(Config {
            query,
            filename,
            case_sensitive,
        })
    }
}
```

#### 直接使用env::args返回的迭代器

文件名: src/main.rs

```plain
fn main() {
    //let args: Vec<String> = env::args().collect();

    let config = Config::new(env::args()).unwrap_or_else(|err| {
        eprintln!("Problem parsing arguments: {}", err);
        process::exit(1);
    });

    // --snip--
}
```

src/lib.rs

```plain
use std::env;
use std::error::Error;
use std::fs;

pub fn run(config: Config) -> Result<(), Box<dyn Error>> {
    //读取文件
    let contents = fs::read_to_string(&config.filename)?;
    // println!("With text:\n{}", contents);
    let results = if config.case_sensitive {
        search(&config.query, &contents)
    } else {
        search_case_insensitive(&config.query, &contents)
    };
    for line in results {
        println!("{}", line);
    }
    Ok(())
}
pub struct Config {
    pub query: String,
    pub filename: String,
    pub case_sensitive: bool,
}
impl Config {
    pub fn new(mut args: std::env::Args) -> Result<Config, &'static str> {
        // if args.len() < 3 {
        //     return Err("not enough arguments");
        // }
        args.next();
        let query = match args.next() {
            Some(arg) => arg,
            None => return Err("Didn't get a query string"),
        };
        let filename = match args.next() {
            Some(arg) => arg,
            None => return Err("Didn't get a file name"),
        };
        // println!("Search for {}", query);
        // println!("In file {}", filename);
        let case_sensitive = env::var("CASE_INSENSITIVE").is_err();
        Ok(Config {
            query,
            filename,
            case_sensitive,
        })
    }
}

pub fn search<'a>(query: &str, contents: &'a str) -> Vec<&'a str> {
    // let mut result = Vec::new();
    // for line in contents.lines() {
    //     if line.contains(query) {
    //         result.push(line);
    //     }
    // }

    // result

    contents
        .lines()
        .filter(|line| line.contains(query))
        .collect()
}
pub fn search_case_insensitive<'a>(query: &str, contents: &'a str) -> Vec<&'a str> {
    // let mut result = Vec::new();
    // let query = query.to_lowercase();
    // for line in contents.lines() {
    //     if line.to_lowercase().contains(&query) {
    //         result.push(line);
    //     }
    // }

    // result
    contents
        .lines()
        .filter(|line| line.to_lowercase().contains(query.to_lowercase().as_str()))
        .collect()
}

#[cfg(test)]
mod tests {
    use super::*;
    #[test]
    fn case_sensitive() {
        let query = "duct";
        let contents = "\
Rust:
safe,fast,productive.
Pick three.";
        assert_eq!(vec!["safe,fast,productive."], search(query, contents));
    }

    #[test]
    fn case_insensitive() {
        let query = "duct";
        let contents = "\
Rust:
safe,fast,productive.
Pick three.";
        assert_eq!(
            vec!["safe,fast,productive."],
            search_case_insensitive(query, contents)
        );
    }
}
```

src/main.rs

```plain
use std::env;
use std::process;
use minigrep::Config;

fn main() {
    // println!("{:?}",args);
    let config = Config::new(env::args()).unwrap_or_else(|err| {
        eprintln!("Problem parsing arguments:{}", err);
        process::exit(1);
    });
    if let Err(e) = minigrep::run(config){
        eprintln!("Application error: {}",e);
        process::exit(1);
    };
}
```

### 性能比较 循环/迭代器

代器是 Rust 的 **零成本抽象**（*zero-cost abstractions*）之一，它意味着抽象并不会引入运行时开销，它与本贾尼·斯特劳斯特卢普（C++ 的设计和实现者）在 “Foundations of C++”（2012） 中所定义的 **零开销**（*zero-overhead*）如出一辙：

In general, C++ implementations obey the zero-overhead principle: What you don’t use, you don’t pay for. And further: What you do use, you couldn’t hand code any better.

- Bjarne Stroustrup "Foundations of C++"

从整体来说，C++ 的实现遵循了零开销原则：你不需要的，无需为他们买单。更有甚者的是：你需要的时候，也不可能找到其他更好的代码了。

- 本贾尼·斯特劳斯特卢普 "Foundations of C++"

# 十八.Cargo 和crates.io

## 采用发布配置自定义构建

**release profile**

- 是预定义的
- 可自定义
- 每个profile配置独立于其它profile

cargo主要的两个profile

- **dev profile**: 适用于开发 cargo build
- **release profile**: 适用于发布 cargo build --release

### 自定义profile

在Cargo.toml中添加[profile.xxxx]区域，在里面覆盖某人配置的子集

文件名: Cargo.toml

```plain
[profile.dev]
opt-level = 0

[profile.release]
opt-level = 3
```

opt-level 设置控制 Rust 会对代码进行何种程度的优化。这个配置的值从 0 到 3。越高的优化级别需要更多的时间编译，所以如果你在进行开发并经常编译，可能会希望在牺牲一些代码性能的情况下编译得快一些。这就是为什么 dev 的 opt-level 默认为 0。

## 文档注释

- 生成HTML文档
- 显示公共Api的文档注释: 如何使用API
- 使用///
- 支持Markdown
- 放置在说明条目前

### 生成文档

运行rustdoc工具

cargo doc

把生成的文档放到target/doc下

### 生成文档并浏览

cargo doc --open

### 常用章节

\#Examples

其它常用章节

```plain
Panics: 函数可能发生panic的场景
Errors: 如果函数返回Result,描述可能的错误种类，以及可导致错误的条件
Safety: 如果函数处于unsafe调用，就应该解释函数unsafe的原因，以及调用者确保的使用前提
```

### 文档注释作为测试

运行cargo test： 把文档注释中的示例代码作为测试来运行

文件名: src/lib.rs

```plain
/// Adds one to the number given.
///
/// # Examples
///
/// ```
/// let arg = 5;
/// let answer = my_crate::add_one(arg);
///
/// assert_eq!(6, answer);
/// ```
pub fn add_one(x: i32) -> i32 {
    x + 1
}
```

尝试 cargo test 运行像示例中 add_one 函数的文档；应该在测试结果中看到像这样的部分：

```plain
Doc-tests my_crate

running 1 test
test src/lib.rs - add_one (line 5) ... ok

test result: ok. 1 passed; 0 failed; 0 ignored; 0 measured; 0 filtered out; finished in 0.27s
```

现在尝试改变函数或例子来使例子中的 assert_eq! 产生 panic。再次运行 cargo test，你将会看到文档测试捕获到了例子与代码不再同步！

### 为包含注释的项添加文档注释

- 符号: //!
- 这类注释通常描述crate和模块

crate root (按惯例src/lib.rs)

一个模块内，将crate或模块作为一个整体进行记录

例子：

文件名: src/lib.rs

```plain
//! # My Crate
//!
//! `my_crate` is a collection of utilities to make performing certain
//! calculations more convenient.

/// Adds one to the number given.
// --snip--
```

## 使用pub use导出方便使用的公共API

你开发时候使用的文件架构可能并不方便用户。你的结构可能是一个包含多个层级的分层结构，不过这对于用户来说并不方便。这是因为想要使用被定义在很深层级中的类型的人可能很难发现这些类型的存在。他们也可能会厌烦要使用 use my_crate::some_module::another_module::UsefulType; 而不是 use my_crate::UsefulType; 来使用类型。

**使用** **pub use** **重导出（re-export）项来使公有结构不同于私有结构**

src/lib.rs

```plain
//! # Art
//!
//! A library for modeling artistic concepts.

pub mod kinds {
    /// The primary colors according to the RYB color model.
    pub enum PrimaryColor {
        Red,
        Yellow,
        Blue,
    }

    /// The secondary colors according to the RYB color model.
    pub enum SecondaryColor {
        Orange,
        Green,
        Purple,
    }
}

pub mod utils {
    use crate::kinds::*;

    /// Combines two primary colors in equal amounts to create
    /// a secondary color.
    pub fn mix(c1: PrimaryColor, c2: PrimaryColor) -> SecondaryColor {
        // --snip--
        SecondaryColor::Green
    }
}
```

文件名: src/main.rs

```plain
use art::kinds::PrimaryColor;
use art::utils::mix;

fn main() {
    let red = PrimaryColor::Red;
    let yellow = PrimaryColor::Yellow;
    mix(red, yellow);
}
```

为了从公有 API 中去掉 crate 的内部组织，我们可以采用示例 中的 art crate 并增加 pub use 语句来重导出项到顶层结构，如示例 14-5 所示：

文件名: src/lib.rs

```plain
//! # Art
//!
//! A library for modeling artistic concepts.

pub use self::kinds::PrimaryColor;
pub use self::kinds::SecondaryColor;
pub use self::utils::mix;

pub mod kinds {
    // --snip--
}

pub mod utils {
    // --snip--
}
```

使用

文件名: src/main.rs

```plain
use art::mix;
use art::PrimaryColor;

fn main() {
    // --snip--
}
```

\### 

## 发布Crate

有了唯一的名称、版本号、由 cargo new 新建项目时增加的作者信息、描述和所选择的 license，已经准备好发布的项目的 *Cargo.toml* 文件可能看起来像这样：

文件名: Cargo.toml

```plain
[package]
name = "guessing_game"
version = "0.1.0"
edition = "2021"
description = "A fun game where you guess what number the computer has chosen."
license = "MIT OR Apache-2.0"
author = "cyberboy"

[dependencies]
```

Cargo 的文档 描述了其他可以指定的元信息，他们可以帮助你的 crate 更容易被发现和使用！

发布：

cargo publish

**crate 一旦发布，就是永久性的:该版本无法覆盖，代码无法删除**

- 目的：依赖于该版本的项目可继续正常工作

## 发布已存在crate的新版本

修改version重新发布

## 使用cargo yank从Crates.io撤回版本

- 不可以删除crate之前的版本

撤回某个版本会阻止新项目开始依赖此版本，不过所有现存此依赖的项目仍然能够下载和依赖这个版本。从本质上说，撤回意味着所有带有 *Cargo.lock* 的项目的依赖不会被破坏，同时任何新生成的 *Cargo.lock* 将不能使用被撤回的版本。

为了撤回一个 crate，运行 cargo yank 并指定希望撤回的版本：

$ cargo yank --vers 1.0.1

也可以撤销撤回操作，并允许项目可以再次开始依赖某个版本，通过在命令上增加 --undo：

$ cargo yank --vers 1.0.1 --undo

撤回 **并没有** 删除任何代码。举例来说，撤回功能并不意在删除不小心上传的秘密信息。如果出现了这种情况，请立即重新设置这些秘密信息。

## Cargo 工作空间（Workspaces）

- cargo 工作空间： 帮助管理多个相互关联且需要协同开发的crate
- cargo 工作空间是一套共享同一个Cargo.lock和输出文件夹的包

### 创建工作空间

[https://kaisery.github.io/trpl-zh-cn/ch14-03-cargo-workspaces.html#cargo-%E5%B7%A5%E4%BD%9C%E7%A9%BA%E9%97%B4](https://kaisery.github.io/trpl-zh-cn/ch14-03-cargo-workspaces.html#cargo-工作空间)

为了在顶层 *add* 目录运行二进制 crate，可以通过 -p 参数和包名称来运行 cargo run 指定工作空间中我们希望使用的包：

```plain
$ cargo run -p adder
    Finished dev [unoptimized + debuginfo] target(s) in 0.0s
     Running `target/debug/adder`
Hello, world! 10 plus one is 11!
```

这会运行 *adder/src/main.rs* 中的代码，其依赖 add_one crate

## 从CRATES.IO安装二进制crate

- 命令: cargo install
- 来源 https://crates.io
- 限制: 只能安装具有二进制目标 (binary target) 的crate

二进制目标binary target:是一个可运行的程序

- 拥有src/main.rs或其它被指定为二进制文件的crate生成

通常: READEME里面有关于crate的描述:

- 拥有library target
- 拥有library target
- 两者兼备

### cargo install

cargo install 安装的二进制存放在根目录的bin文件夹

## 使用自定义命令扩展cargo

- cargo被设计成可以使用子命令来扩展
- 例：如果$PATH中的某个二进制是cargo-something，你可以像子命令一样运行:

cargo something

- 类似这样的自定义命令可以通过该命令列出: cargo --list
- 优点: 可以使用cargo install来安装扩展，像内置工具一样来运行

# 十九.智能指针

- **指针** （*pointer*）是一个包含内存地址的变量的通用概念。

这个地址引用，或 “指向”（points at）一些其他数据。

- Rust 中最常见的指针是 **引用**（*reference*）。

引用以 & 符号为标志并借用了他们所指向的值。除了引用数据没有任何其他特殊功能。它们也**没有任何额外开销**，所以应用得最多。

- **智能指针**（*smart pointers*）是一类数据结构，他们的表现类似指针，但是也拥有额外的元数据和功能。

## 引用和智能指针的其它不同

- 引用：只借用数据
- 智能指针：很多时候都拥有它所指向的数据

**智能指针的例子：**

- String 和 Vec<T>
- 都拥有一片内存区域，且允许用户对其操作
- 还拥有元数据（例如容量等）
- 提供额外的功能或保障（String保障其数据是合法的UTF-8编码）

**智能指针的实现**

- 智能指针通常使用struct实现，并且实现了：

Deref和Drop这两个trait

- Deref trait 允许智能指针struct的实例像引用一样使用
- Drop trait 允许你自定义当智能指针实例走出作用域时的代码

## 使用Box<T>指向堆上的数据

- Box<T>是最简单的智能指针：

- - 允许你在heap上存储数据（而不是stack）
  - stack上是指向heap数据的指针
  - 没有性能开销
  - 没有其它额外功能

- Box<T>实现了Deref trait和Drop trait

多用于如下场景：

- 当有一个在编译时未知大小的类型，而又想要在需要确切大小的上下文中使用这个类型值的时候
- 当有大量数据并希望在确保数据不被拷贝的情况下转移所有权的时候
- 当希望拥有一个值并只关心它的类型是否实现了特定 trait 而不是其具体类型的时候

```plain
fn main() {
    let b = Box::new(5);
    println!("b = {}",b);
}
```

## 使用Box赋能递归类型

- 在编译时,Rust需要知道一个类型所占的空间大小
- 而递归类型的大小无法在编译时确定
- 但Box类型的大小确定
- 在递归类型中使用Box就可以解决上述问题
- 函数式语言的Cons List

## Cons List

*cons list* 是一个来源于 Lisp 编程语言及其方言的数据结构。在 Lisp 中，cons 函数（“construct function" 的缩写）利用两个参数来构造一个新的列表，他们通常是一个单独的值和另一个列表。

cons 函数的概念涉及到更常见的函数式编程术语；“将 *x* 与 *y* 连接” 通常意味着构建一个新的容器而将 *x* 的元素放在新容器的开头，其后则是容器 *y* 的元素。

cons list 的每一项都包含两个元素：当前项的值和下一项。其最后一项值包含一个叫做 Nil 的值且没有下一项。cons list 通过递归调用 cons 函数产生。代表递归的终止条件（base case）的规范名称是 Nil，它宣布列表的终止。

代表递归的终止条件（base case）的规范名称是 Nil，它宣布列表的终止。注意这不同于 “null” 或 “nil” 的概念，他们代表无效或缺失的值。

Cons List并不是Rust的常用集合

```plain
use crate::List::{Cons,Nil};
fn main() {
    let list = Cons(1, Cons(2, Cons(3, Nil)));
}

enum List {
    Cons(i32,List),
    Nil,
}
```

运行报错

```plain
Compiling my_box v0.1.0 (C:\Users\cauchy\Desktop\rust\my_box)
error[E0072]: recursive type `List` has infinite size
 --> src\main.rs:6:1
  |
6 | enum List {
  | ^^^^^^^^^
7 |     Cons(i32,List),
  |              ---- recursive without indirection
  |
help: insert some indirection (e.g., a `Box`, `Rc`, or `&`) to break the cycle
  |
7 |     Cons(i32,Box<List>),
  |              ++++    +

For more information about this error, try `rustc --explain E0072`.
error: could not compile `my_box` due to previous error
```

## 计算非递归类型的大小

Message 枚举：

```plain
enum Message {
    Quit,
    Move { x: i32, y: i32 },
    Write(String),
    ChangeColor(i32, i32, i32),
}
```

当 Rust 需要知道要为 Message 值分配多少空间时，它可以检查每一个成员并发现 

- Message::Quit 并不需要任何空间，
- Message::Move 需要足够储存两个 i32 值的空间，依此类推。
- 因为 enum 实际上只会使用其中的一个成员，所以 Message 值所需的空间等于储存其最大成员的空间大小。

与此相对当 Rust 编译器检查像上例的 List 这样的递归类型时会发生什么呢。编译器尝试计算出储存一个 List 枚举需要多少内存，并开始检查 Cons 成员，那么 Cons 需要的空间等于 i32 的大小加上 List 的大小。为了计算 List 需要多少内存，它检查其成员，从 Cons 成员开始。Cons成员储存了一个 i32 值和一个List值，这样的计算将无限进行下去

## 使用Box<T>给递归类型一个已知的大小

- 因为 Box<T> 是一个指针，我们总是知道它需要多少空间

----- 指针的大小并不会根据其指向的数据量而改变。

- Box<T>

1. 只提供了”间接”存储和heap内存分配的功能
2. 没有其它额外功能
3. 没有性能开销
4. 适用于需要间接存储的场景，例如Cons List
5. 实现了Deref trait和Drop trait

## Dref Trait

- 实现Deref Trait使我们可以**自定义解引用运算符\*的行为**
- 通过实现Deref，智能指针可**像引用一样来处理**

## 解引用运算符

文件名: src/main.rs

```plain
fn main() {
    let x = 5;
    let y = &x;

    assert_eq!(5, x);
    assert_eq!(5, *y);
}
```

## 把Box<T>当作引用

文件名: src/main.rs

```plain
fn main() {
    let x = 5;
    let y = Box::new(x);

    assert_eq!(5, x);
    assert_eq!(5, *y);
}
```

## 定义自己的智能指针

文件名: src/main.rs

```plain
struct MyBox<T>(T);

impl<T> MyBox<T> {
    fn new(x: T) -> MyBox<T> {
        MyBox(x)
    }
}
fn main() {
    let x = 5;
    let y = MyBox::new(x);

    assert_eq!(5, x);
    assert_eq!(5, *y);
}
```

得到的编译错误是：

```plain
$ cargo run
   Compiling deref-example v0.1.0 (file:///projects/deref-example)
error[E0614]: type `MyBox<{integer}>` cannot be dereferenced
  --> src/main.rs:14:19
   |
14 |     assert_eq!(5, *y);
   |                   ^^

For more information about this error, try `rustc --explain E0614`.
error: could not compile `deref-example` due to previous error
```

MyBox<T> 类型不能解引用，因为我们尚未在该类型实现这个功能。为了启用 * 运算符的解引用功能，需要实现 Deref trait。

## 通过实现 Deref trait 将某类型像引用一样处理

- 标准库中的Deref trait要求我们实现一个deref方法：

-- 该方法借用self

-- 返回一个指向内部数据的引用

文件名: src/main.rs

```plain
use std::ops::Deref;

impl<T> Deref for MyBox<T> {
    type Target = T;

    fn deref(&self) -> &Self::Target {
        &self.0
    }
}
```

当我们在示例代码

```plain
fn main() {
    let x = 5;
    let y = MyBox::new(x);

    assert_eq!(5, x);
    assert_eq!(5, *y);
}
```

输入 *y 时，Rust 事实上在底层运行了如下代码：

*(y.deref())

## 函数和方法的隐式解引用转化（Deref Coercion）

- Deref Coercion 是为**函数和方法**提供的一种便捷特性
- 假设T实现了Deref trait: Deref Coercion可以把T的引用转化为T经过Deref操作后生成的引用
- 当把某类型的引用传递给函数或方法时，但它的类型于定义的参数类型不匹配:

1. Deref Coercion就会自动发生
2. 编译器会对deref进行一系列调用，来把它转为所需的参数类型
3. 它在编译时完成，没有额外性能开销

文件名: src/main.rs

```plain
fn hello(name: &str) {
    println!("Hello, {}!", name);
}
fn main() {
    let m = MyBox::new(String::from("Rust"));
    //&m &MyBox<String>
    //deref &String
    //&String &str
    hello(&m);
}
```

1. 这里使用 &m 调用 hello 函数，其为 MyBox<String> 值的引用
2. 因为示例 中在 MyBox<T> 上实现了 Deref trait，Rust 可以通过 deref 调用将 &MyBox<String> 变为 &String。
3. 标准库中提供了 String 上的 Deref 实现，其会返回字符串 slice，这可以在 Deref 的 API 文档中看到。Rust 再次调用 deref 将 &String 变为 &str，这就符合 hello 函数的定义了。

如果 Rust 没有实现 Deref 强制转换，为了使用 &MyBox<String> 类型的值调用 hello，则不得不编写以下的代码

文件名: src/main.rs

```plain
fn main() {
    let m = MyBox::new(String::from("Rust"));
    hello(&(*m)[..]);
}
```

## 解引用与可变性

- 可使用DerefMut trait重载可变引用的*运算符
- 在类型和trait在下列三种情况发生时，Rust会执行deref coercion:

1. 当T: Deref<Target=U>，允许&T 转换为&U
2. 当T: Deref<Target=U>，允许&mut T转换为&mut U
3. 当T: Deref<Target=U>，允许&mut T转换为&U

## Drop Trait

- 实现Drop Trait 可以让我们自定义**当值将要离开作用域时发生的动作**

1. 例如：文件，网络资源释放等
2. 任何类型都可以实现Drop trait

- Drop Trait只要求你实现drop 方法

1. 参数： 对self的可变引用

- Drop trait 在预导入模块里

文件名: src/main.rs

```plain
struct CustomSmartPointer {
    data: String,
}

impl Drop for CustomSmartPointer {
    fn drop(&mut self) {
        println!("Dropping CustomSmartPointer with data `{}`!", self.data);
    }
}

fn main() {
    let c = CustomSmartPointer {
        data: String::from("my stuff"),
    };
    let d = CustomSmartPointer {
        data: String::from("other stuff"),
    };
    println!("CustomSmartPointers created.");
}
```

当运行这个程序，会出现如下输出：

```plain
$ cargo run
   Compiling drop-example v0.1.0 (file:///projects/drop-example)
    Finished dev [unoptimized + debuginfo] target(s) in 0.60s
     Running `target/debug/drop-example`
CustomSmartPointers created.
Dropping CustomSmartPointer with data `other stuff`!
Dropping CustomSmartPointer with data `my stuff`!
```

### 使用std::mem::drop来提前drop值

- 很难直接禁用自动的drop功能，也没必要

1. Drop trait的目的就是进行自动的释放处理逻辑

- Rust 不允许手动调用Drop trait的drop方法
- 但可以调用标准库的std::mem::drop函数(prelude)，来提前drop值

文件名: src/main.rs

```plain
fn main() {
    let c = CustomSmartPointer {
        data: String::from("some data"),
    };
    println!("CustomSmartPointer created.");
    drop(c);
    println!("CustomSmartPointer dropped before the end of main.");
}
```

运行这段代码会打印出如下：

```plain
$ cargo run
   Compiling drop-example v0.1.0 (file:///projects/drop-example)
    Finished dev [unoptimized + debuginfo] target(s) in 0.73s
     Running `target/debug/drop-example`
CustomSmartPointer created.
Dropping CustomSmartPointer with data `some data`!
CustomSmartPointer dropped before the end of main.
```

我们也无需担心意外的清理掉仍在使用的值，这会造成编译器错误：所有权系统确保引用总是有效的，也会确保 drop 只会在值不再被使用时被调用一次。

## Rc<T>引用计数智能指针

- 有时一个值会有多个所有者
- 为了支持多重所有权: Rt<T>

1. reference counting(引用计数)
2. 追踪到值得引用
3. 0个引用:该值可以被清理掉

- 需要在heap上分配数据，这些数据被程序得多个部分读取（只读），但在编译时无法确定哪个部分最后使用完这些数据
- 注意 Rc<T> 只能用于单线程场景；

我们希望创建两个共享第三个列表所有权的列表，其概念将会看起来如图所示：

文件名: src/main.rs

不能用两个 Box<T> 的列表尝试共享第三个列表的所有权

```plain
enum List {
    Cons(i32, Box<List>),
    Nil,
}

use crate::List::{Cons, Nil};

fn main() {
    let a = Cons(5, Box::new(Cons(10, Box::new(Nil))));
    let b = Cons(3, Box::new(a));
    let c = Cons(4, Box::new(a));
}
```

我们修改 List 的定义为使用 Rc<T> 代替 Box<T>，如列表 所示。现在每一个 Cons 变量都包含一个值和一个指向 List 的 Rc<T>。当创建 b 时，不同于获取 a 的所有权，这里会克隆 a 所包含的 Rc<List>，这会将引用计数从 1 增加到 2 并允许 a 和 b 共享 Rc<List> 中数据的所有权。创建 c 时也会克隆 a，这会将引用计数从 2 增加为 3。每次调用 Rc::clone，Rc<List> 中数据的引用计数都会增加，直到有零个引用之前其数据都不会被清理。

文件名: src/main.rs

```plain
enum List {
    Cons(i32, Rc<List>),
    Nil,
}

use crate::List::{Cons, Nil};
use std::rc::Rc;

fn main() {
    let a = Rc::new(Cons(5, Rc::new(Cons(10, Rc::new(Nil)))));
    let b = Cons(3, Rc::clone(&a));
    let c = Cons(4, Rc::clone(&a));
}
```

也可以调用 a.clone() 而不是 Rc::clone(&a)，不过在这里 Rust 的习惯是使用 Rc::clone。

- Rc::clone 的实现并不像大部分类型的 clone 实现那样对所有数据进行深拷贝。
- Rc::clone 只会增加引用计数，这并不会花费多少时间。深拷贝可能会花费很长时间。

## 克隆Rc<T>会增加引用计数

文件名: src/main.rs

Rc::strong_count获得引用计数

```plain
fn main() {
    let a = Rc::new(Cons(5, Rc::new(Cons(10, Rc::new(Nil)))));
    println!("count after creating a = {}", Rc::strong_count(&a));
    let b = Cons(3, Rc::clone(&a));
    println!("count after creating b = {}", Rc::strong_count(&a));
    {
        let c = Cons(4, Rc::clone(&a));
        println!("count after creating c = {}", Rc::strong_count(&a));
    }
    println!("count after c goes out of scope = {}", Rc::strong_count(&a));
}
```

这段代码会打印出：

```plain
$ cargo run
   Compiling cons-list v0.1.0 (file:///projects/cons-list)
    Finished dev [unoptimized + debuginfo] target(s) in 0.45s
     Running `target/debug/cons-list`
count after creating a = 1
count after creating b = 2
count after creating c = 3
count after c goes out of scope = 2
```

我们能够看到 a 中 Rc<List> 的初始引用计数为1，接着每次调用 clone，计数会增加1。当 c 离开作用域时，计数减1。不必像调用 Rc::clone 增加引用计数那样调用一个函数来减少计数；Drop trait 的实现当 Rc<T> 值离开作用域时自动减少引用计数。

从这个例子我们所不能看到的是，在 main 的结尾当 b 然后是 a 离开作用域时，此处计数会是 0，同时 Rc<List> 被完全清理。使用 Rc<T> 允许一个值有多个所有者，引用计数则确保只要任何所有者依然存在其值也保持有效。

- Rc<T>通过**不可变引用**， Rc<T> 允许在程序的多个部分之间只读地共享数据。
- 如果 Rc<T> 也允许多个可变引用，则会违反第四章讨论的借用规则之一：相同位置的多个可变借用可能造成数据竞争和不一致。

## RefCell<T>和内部可变性

### 内部可变性（interior mutability)

- interior mutability是Rust的设计模式之一
- 它允许你在支持有不可变引用的前提下对数据进行修改

数据结构中使用了unsafe代码来绕过Rust正常的可变性和借用规则

- 与Rc<T>不同，RefCell<T>类型代表了其持有数据的唯一所有权

回忆借用规则：

1. 在任何给定的时间里，你要么只能拥有一个可变引用，要么只能拥有任意数量的不可变引用
2. 引用总是有效的

### RefCell<T>与Box<T>的区别

| **Box****<****T****>**           | **RefCell****<****T****>**   |
| -------------------------------- | ---------------------------- |
| **编译阶段**强制代码遵守借用规则 | 只会在**运行时**检查借用规则 |
| 否则出现错误                     | 否则触发panic                |

### 借用规则在不同阶段进行检查的比较

| **编译阶段**           | **运行时**                                           |
| ---------------------- | ---------------------------------------------------- |
| 尽早暴露问题           | 问题暴露延后，甚至到生产环境                         |
| 没有任何运行时开销     | 因借用计数产生些许性能损失                           |
| 对大多数场景是最佳选择 | 实现某些特定的内存安全场景(不可变环境中修改自身数据) |
| 是Rust的默认行为       |                                                      |

- 与Rc<T>类似，只能用于**单线程**场景

### 选择Box<T>，Rc<T>，RefCell<T>的依据

|                  | **Box****<****T****>**       | **Rc****<****T****>**  | **RefCell****<****T****>**     |
| ---------------- | ---------------------------- | ---------------------- | ------------------------------ |
| 同一数据的所有者 | 一个                         | 多个                   | 一个                           |
| 可变性,借用检查  | 可变，不可变借用(编译时检查) | 不可变借用(编译时检查) | 可变，不可变借用（运行时检查） |

### 内部可变性：可变的借用一个不可变的值

借用规则的一个推论是当有一个不可变值时，不能可变地借用它。例如，如下代码不能编译：

```plain
fn main() {
    let x = 5;
    let y = &mut x;
}
```

如果尝试编译，会得到如下错误：

```plain
$ cargo run
   Compiling borrowing v0.1.0 (file:///projects/borrowing)
error[E0596]: cannot borrow `x` as mutable, as it is not declared as mutable
 --> src/main.rs:3:13
```

如下是一个我们想要测试的场景：我们在编写一个记录某个值与最大值的差距的库，并根据当前值与最大值的差距来发送消息。例如，这个库可以用于记录用户所允许的 API 调用数量限额。

该库只提供记录与最大值的差距，以及何种情况发送什么消息的功能。使用此库的程序则期望提供实际发送消息的机制：程序可以选择记录一条消息、发送 email、发送短信等等。库本身无需知道这些细节；只需实现其提供的 Messenger trait 即可。示例 15-20 展示了库代码：

文件名: src/lib.rs

```plain
pub trait Messenger {
    fn send(&self, msg: &str);
}

pub struct LimitTracker<'a, T: Messenger> {
    messenger: &'a T,
    value: usize,
    max: usize,
}

impl<'a, T> LimitTracker<'a, T>
where
    T: Messenger,
{
    pub fn new(messenger: &T, max: usize) -> LimitTracker<T> {
        LimitTracker {
            messenger,
            value: 0,
            max,
        }
    }

    pub fn set_value(&mut self, value: usize) {
        self.value = value;

        let percentage_of_max = self.value as f64 / self.max as f64;

        if percentage_of_max >= 1.0 {
            self.messenger.send("Error: You are over your quota!");
        } else if percentage_of_max >= 0.9 {
            self.messenger
                .send("Urgent warning: You've used up over 90% of your quota!");
        } else if percentage_of_max >= 0.75 {
            self.messenger
                .send("Warning: You've used up over 75% of your quota!");
        }
    }
}
#[cfg(test)]
mod tests {
    use super::*;

    struct MockMessenger {
        sent_messages: Vec<String>,
    }

    impl MockMessenger {
        fn new() -> MockMessenger {
            MockMessenger {
                sent_messages: vec![],
            }
        }
    }

    impl Messenger for MockMessenger {
        fn send(&self, message: &str) {
            self.sent_messages.push(String::from(message));
        }
    }

    #[test]
    fn it_sends_an_over_75_percent_warning_message() {
        let mock_messenger = MockMessenger::new();
        let mut limit_tracker = LimitTracker::new(&mock_messenger, 100);

        limit_tracker.set_value(80);

        assert_eq!(mock_messenger.sent_messages.len(), 1);
    }
}
```

这些代码中一个重要部分是拥有一个方法 send 的 Messenger trait，其获取一个 self 的**不可变引用**和文本信息。这个 trait 是 mock 对象所需要实现的接口库，这样 mock 就能像一个真正的对象那样使用了。另一个重要的部分是我们需要测试 LimitTracker 的 set_value 方法的行为。可以改变传递的 value 参数的值，不过 set_value 并没有返回任何可供断言的值。也就是说，如果使用某个实现了 Messenger trait 的值和特定的 max 创建 LimitTracker，当传递不同 value 值时，消息发送者应被告知发送合适的消息。

我们所需的 mock 对象是，调用 send 并不实际发送 email 或消息，而是只记录信息被通知要发送了。可以新建一个 mock 对象实例，用其创建 LimitTracker，调用 LimitTracker 的 set_value 方法，然后检查 mock 对象是否有我们期望的消息。示例 15-21 展示了一个如此尝试的 mock 对象实现，不过借用检查器并不允许：

然而，这个测试是有问题的：

```plain
$ cargo test
   Compiling limit-tracker v0.1.0 (file:///projects/limit-tracker)
error[E0596]: cannot borrow `self.sent_messages` as mutable, as it is behind a `&` reference
  --> src/lib.rs:58:13
   |
2  |     fn send(&self, msg: &str);
   |             ----- help: consider changing that to be a mutable reference: `&mut self`
...
58 |             self.sent_messages.push(String::from(message));
   |             ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^ `self` is a `&` reference, so the data it refers to cannot be borrowed as mutable

For more information about this error, try `rustc --explain E0596`.
error: could not compile `limit-tracker` due to previous error
warning: build failed, waiting for other jobs to finish...
error: build failed
```

不能修改 MockMessenger 来记录消息，因为 send 方法获取了 self 的不可变引用。我们也不能参考错误文本的建议使用 &mut self 替代，因为这样 send 的签名就不符合 Messenger trait 定义中的签名了（可以试着这么改，看看会出现什么错误信息）。

这正是内部可变性的用武之地！我们将通过 RefCell 来储存 sent_messages，然后 send 将能够修改 sent_messages 并储存消息。

文件名: src/lib.rs

```plain
#[cfg(test)]
mod tests {
    use super::*;
    use std::cell::RefCell;

    struct MockMessenger {
        sent_messages: RefCell<Vec<String>>,
    }

    impl MockMessenger {
        fn new() -> MockMessenger {
            MockMessenger {
                sent_messages: RefCell::new(vec![]),
            }
        }
    }

    impl Messenger for MockMessenger {
        fn send(&self, message: &str) {
            self.sent_messages.borrow_mut().push(String::from(message));
        }
    }

    #[test]
    fn it_sends_an_over_75_percent_warning_message() {
        // --snip--

        assert_eq!(mock_messenger.sent_messages.borrow().len(), 1);
    }
}
```

### 使用RefCell<T>在运行时记录借用信息

- 两个方法（安全接口）

1. borrow 方法：返回智能指针Ref<T>，它实现了Deref
2. borrow_mut方法：返回RefMut<T>，它实现了Deref

- RefCell<T>会记录当前存在多少个活跃的Ref<T>和RefMut<T>智能指针

1. 每次调用borrow：不可变借用计数+1
2. 任何一个Ref<T>的值离开作用域被释放时：不可变借用计数-1
3. 每次调用borrow_mut: 可变借用计数+1
4. 任何一个RefMut<T>的值利开作用域被释放时：可变借用计数-1

- Rust以此计数来维护借用检查规则：

任何一个给定时间里，只允许拥有多个不可变借用或一个可变借用

### 结合 Rc 和 RefCell 来拥有多个可变数据所有者

文件名: src/main.rs

```plain
#[derive(Debug)]
enum List {
    Cons(Rc<RefCell<i32>>, Rc<List>),
    Nil,
}

use crate::List::{Cons, Nil};
use std::cell::RefCell;
use std::rc::Rc;

fn main() {
    let value = Rc::new(RefCell::new(5));

    let a = Rc::new(Cons(Rc::clone(&value), Rc::new(Nil)));

    let b = Cons(Rc::new(RefCell::new(3)), Rc::clone(&a));
    let c = Cons(Rc::new(RefCell::new(4)), Rc::clone(&a));

    *value.borrow_mut() += 10;

    println!("a after = {:?}", a);
    println!("b after = {:?}", b);
    println!("c after = {:?}", c);
}
```

当我们打印出 a、b 和 c 时，可以看到他们都拥有修改后的值 15 而不是 5：

```plain
$ cargo run
   Compiling cons-list v0.1.0 (file:///projects/cons-list)
    Finished dev [unoptimized + debuginfo] target(s) in 0.63s
     Running `target/debug/cons-list`
a after = Cons(RefCell { value: 15 }, Nil)
b after = Cons(RefCell { value: 3 }, Cons(RefCell { value: 15 }, Nil))
c after = Cons(RefCell { value: 4 }, Cons(RefCell { value: 15 }, Nil))
```

## 其它可实现内部可变性的类型

- Cell<T>：通过复制来访问数据
- Mutex<T>：用于实现跨线程的情形下的内部可变性模式

## 循环引用导致内存泄漏

Rust 的内存安全性保证使其难以意外地制造永远也不会被清理的内存（被称为 **内存泄漏**（*memory leak*）），但并不是不可能。与在编译时拒绝数据竞争不同， Rust 并不保证完全地避免内存泄漏，这意味着内存泄漏在 Rust 被认为是内存安全的。这一点可以通过 Rc<T> 和 RefCell<T> 看出：创建引用循环的可能性是存在的。这会造成内存泄漏，因为每一项的引用计数永远也到不了 0，其值也永远不会被丢弃。

文件名: src/main.rs

```plain
use crate::List::{Cons, Nil};
use std::cell::RefCell;
use std::rc::Rc;

#[derive(Debug)]
enum List {
    Cons(i32, RefCell<Rc<List>>),
    Nil,
}

impl List {
    fn tail(&self) -> Option<&RefCell<Rc<List>>> {
        match self {
            Cons(_, item) => Some(item),
            Nil => None,
        }
    }
}

fn main() {
    let a = Rc::new(Cons(5, RefCell::new(Rc::new(Nil))));

    println!("a initial rc count = {}", Rc::strong_count(&a));
    println!("a next item = {:?}", a.tail());

    let b = Rc::new(Cons(10, RefCell::new(Rc::clone(&a))));

    println!("a rc count after b creation = {}", Rc::strong_count(&a));
    println!("b initial rc count = {}", Rc::strong_count(&b));
    println!("b next item = {:?}", b.tail());

    if let Some(link) = a.tail() {
        *link.borrow_mut() = Rc::clone(&b);
    }

    println!("b rc count after changing a = {}", Rc::strong_count(&b));
    println!("a rc count after changing a = {}", Rc::strong_count(&a));

    // Uncomment the next line to see that we have a cycle;
    // it will overflow the stack
    // println!("a next item = {:?}", a.tail());
}
```

如果保持最后的 println! 行注释并运行代码，会得到如下输出：

```plain
$ cargo run
   Compiling cons-list v0.1.0 (file:///projects/cons-list)
    Finished dev [unoptimized + debuginfo] target(s) in 0.53s
     Running `target/debug/cons-list`
a initial rc count = 1
a next item = Some(RefCell { value: Nil })
a rc count after b creation = 2
b initial rc count = 1
b next item = Some(RefCell { value: Cons(5, RefCell { value: Nil }) })
b rc count after changing a = 2
a rc count after changing a = 2
```

如果取消最后 println! 的注释并运行程序，Rust 会尝试打印出 a 指向 b 指向 a 这样的循环直到栈溢出。

**防止内存泄漏的解决办法**

- 依靠开发者来保证，不能依靠Rust
- 重新组织数据结构：一些引用来表达所有权，一些引用不表达所有权

1. 循环引用中的一部分具有所有权关系，另一部分不涉及所有权关系
2. 而只有所有权关系才影响值的清理

### 避免引用循环：将 Rc<T> 变为 Weak<T>

- Rc::clone为Rc<T>实例的strong_count加1，Rc<T>的实例只有在strong_count为0时才会被清理
- Rc<T>实例通过调用Rc::downgrade方法可以创建值的Weak Reference（弱引用）

1. 返回类型是Weak<T>（智能指针）
2. 调用Rc::downgrade会为weak_count加1

- Rc<T>使用weak_count来追踪存在多少Weak<T>
- weak_count不为0并不影响Rc<T>实例的清理

### Strong VS Weak

- Strong Reference 是关于如何分享Rc<T>实例的所有权
- Weak Reference 并不表达上述意思
- 使用Weak Reference并不会创建循环引用：

当Strong Reference数量为0的时候，Weak Reference会自动断开

- 在使用Weak<T>前，需保证它指向的值仍然存在：

在Weak<T>实例上调用upgrade方法，返回Option<Rc<T>>

#### 创建树形数据结构：带有子节点的 Node

文件名: src/main.rs

```plain
use std::cell::RefCell;
use std::rc::Rc;

#[derive(Debug)]
struct Node {
    value: i32,
    children: RefCell<Vec<Rc<Node>>>,
}
fn main() {
    let leaf = Rc::new(Node {
        value: 3,
        children: RefCell::new(vec![]),
    });

    let branch = Rc::new(Node {
        value: 5,
        children: RefCell::new(vec![Rc::clone(&leaf)]),
    });
```

这里克隆了 leaf 中的 Rc<Node> 并储存在了 branch 中，这意味着 leaf 中的 Node 现在有两个所有者：leaf和branch。可以通过 branch.children 从 branch 中获得 leaf，不过无法从 leaf 到 branch。leaf 没有到 branch 的引用且并不知道他们相互关联。我们希望 leaf 知道 branch 是其父节点。稍后我们会这么做

#### 增加从子到父的引用

为了使子节点知道其父节点，需要在 Node 结构体定义中增加一个 parent 字段。问题是 parent 的类型应该是什么。我们知道其不能包含 Rc<T>，因为这样 leaf.parent 将会指向 branch 而 branch.children 会包含 leaf 的指针，这会形成引用循环，会造成其 strong_count 永远也不会为 0。

现在换一种方式思考这个关系:

- 父节点应该拥有其子节点：
- 如果父节点被丢弃了，其子节点也应该被丢弃。
- 然而子节点不应该拥有其父节点：
- 如果丢弃子节点，其父节点应该依然存在。

这正是弱引用的例子！

所以 parent 使用 Weak<T> 类型而不是 Rc<T>，具体来说是 RefCell<Weak<Node>>。现在 Node 结构体定义看起来像这样：

文件名: src/main.rs

```plain
use std::cell::RefCell;
use std::rc::{Rc, Weak};

#[derive(Debug)]
struct Node {
    value: i32,
    parent: RefCell<Weak<Node>>,
    children: RefCell<Vec<Rc<Node>>>,
}
fn main() {
    let leaf = Rc::new(Node {
        value: 3,
        parent: RefCell::new(Weak::new()),
        children: RefCell::new(vec![]),
    });

    println!("leaf parent = {:?}", leaf.parent.borrow().upgrade());

    let branch = Rc::new(Node {
        value: 5,
        parent: RefCell::new(Weak::new()),
        children: RefCell::new(vec![Rc::clone(&leaf)]),
    });

    *leaf.parent.borrow_mut() = Rc::downgrade(&branch);

    println!("leaf parent = {:?}", leaf.parent.borrow().upgrade());
}
```

创建 leaf 节点类似于示例 15-27 中如何创建 leaf 节点的，除了 parent 字段有所不同：leaf 开始时没有父节点，所以我们新建了一个空的 Weak 引用实例。

此时，当尝试使用 upgrade 方法获取 leaf 的父节点引用时，会得到一个 None 值。如第一个 println! 输出所示：

leaf parent = None

当创建 branch 节点时，其也会新建一个 Weak<Node> 引用，因为 branch 并没有父节点。leaf 仍然作为 branch 的一个子节点。一旦在 branch 中有了 Node 实例，就可以修改 leaf 使其拥有指向父节点的 Weak<Node> 引用。这里使用了 leaf 中 parent 字段里的 RefCell<Weak<Node>> 的 borrow_mut 方法，接着使用了 Rc::downgrade 函数来从 branch 中的 Rc<Node> 值创建了一个指向 branch 的 Weak<Node> 引用。

当再次打印出 leaf 的父节点时，这一次将会得到存放了 branch 的 Some 值：现在 leaf 可以访问其父节点了！当打印出 leaf 时，我们也避免了如示例 15-26 中最终会导致栈溢出的循环：Weak<Node> 引用被打印为 (Weak)：

```plain
leaf parent = Some(Node { value: 5, parent: RefCell { value: (Weak) },
children: RefCell { value: [Node { value: 3, parent: RefCell { value: (Weak) },
children: RefCell { value: [] } }] } })
```

没有无限的输出表明这段代码并没有造成引用循环。这一点也可以从观察 Rc::strong_count 和 Rc::weak_count 调用的结果看出。

**可视化strong_count和weak_count的改变**让我们通过创建了一个新的内部作用域并将 branch 的创建放入其中，来观察 Rc<Node> 实例的 strong_count 和 weak_count 值的变化。这会展示当 branch 创建和离开作用域被丢弃时会发生什么。这些修改如示例 15-29 所示：

文件名: src/main.rs

```plain
fn main() {
    let leaf = Rc::new(Node {
        value: 3,
        parent: RefCell::new(Weak::new()),
        children: RefCell::new(vec![]),
    });

    println!(
        "leaf strong = {}, weak = {}",
        Rc::strong_count(&leaf),
        Rc::weak_count(&leaf),
    );

    {
        let branch = Rc::new(Node {
            value: 5,
            parent: RefCell::new(Weak::new()),
            children: RefCell::new(vec![Rc::clone(&leaf)]),
        });

        *leaf.parent.borrow_mut() = Rc::downgrade(&branch);

        println!(
            "branch strong = {}, weak = {}",
            Rc::strong_count(&branch),
            Rc::weak_count(&branch),
        );

        println!(
            "leaf strong = {}, weak = {}",
            Rc::strong_count(&leaf),
            Rc::weak_count(&leaf),
        );
    }

    println!("leaf parent = {:?}", leaf.parent.borrow().upgrade());
    println!(
        "leaf strong = {}, weak = {}",
        Rc::strong_count(&leaf),
        Rc::weak_count(&leaf),
    );
}
```

示例 15-29：在内部作用域创建 branch 并检查其强弱引用计数

一旦创建了 leaf，其 Rc<Node> 的强引用计数为 1，弱引用计数为 0。在内部作用域中创建了 branch 并与 leaf 相关联，此时 branch 中 Rc<Node> 的强引用计数为 1，弱引用计数为 1（因为 leaf.parent 通过 Weak<Node> 指向 branch）。这里 leaf 的强引用计数为 2，因为现在 branch 的 branch.children 中储存了 leaf 的 Rc<Node> 的拷贝，不过弱引用计数仍然为 0。

当内部作用域结束时，branch 离开作用域，Rc<Node> 的强引用计数减少为 0，所以其 Node 被丢弃。来自 leaf.parent 的弱引用计数 1 与 Node 是否被丢弃无关，所以并没有产生任何内存泄漏！

如果在内部作用域结束后尝试访问 leaf 的父节点，会再次得到 None。在程序的结尾，leaf 中 Rc<Node> 的强引用计数为 1，弱引用计数为 0，因为现在 leaf 又是 Rc<Node> 唯一的引用了。

所有这些管理计数和值的逻辑都内建于 Rc<T> 和 Weak<T> 以及它们的 Drop trait 实现中。通过在 Node 定义中指定从子节点到父节点的关系为一个Weak<T>引用，就能够拥有父节点和子节点之间的双向引用而不会造成引用循环和内存泄漏。

# 二十.无畏并发

- **并发编程**（*Concurrent programming*），代表程序的不同部分相互独立的执行，
- **并行编程**（*parallel programming*）代表程序不同部分于同时执行

## 使用线程同时运行代码

在大部分现代操作系统中，已执行程序的代码在一个 **进程**（*process*）中运行，操作系统则负责管理多个进程。在程序内部，也可以拥有多个同时运行的独立部分。运行这些独立部分的功能被称为 **线程**（*threads*）。

将程序中的计算拆分进多个线程可以改善性能，因为程序可以同时进行多个任务，不过这也会增加复杂性。因为线程是同时运行的，所以无法预先保证不同线程中的代码的执行顺序。这会导致诸如此类的问题：

- 竞态条件（Race conditions），多个线程以不一致的顺序访问数据或资源
- 死锁（Deadlocks），两个线程相互等待对方停止使用其所拥有的资源，这会阻止它们继续运行
- 只会发生在特定情况且难以稳定重现和修复的 bug

编程语言有一些不同的方法来实现线程。

- 很多操作系统提供了创建新线程的 API。这种由编程语言调用操作系统 API 创建线程的模型有时被称为 *1:1*，一个 OS 线程对应一个语言线程。**Rust 标准库只提供了 1:1 线程实现**；需要较小的运行时
- 有一些 crate 实现了其他有着不同取舍的线程模型，即语言自己实现的线程（绿色线程）：M:N模型。需要较大的运行时

## 通过spawn创建新线程

为了创建一个新线程，需要调用 thread::spawn 函数并传递一个闭包，并在其中包含希望在新线程运行的代码

文件名: src/main.rs

```plain
use std::thread;
use std::time::Duration;

fn main() {
    thread::spawn(|| {
        for i in 1..10 {
            println!("hi number {} from the spawned thread!", i);
            thread::sleep(Duration::from_millis(1));
        }
    });

    for i in 1..5 {
        println!("hi number {} from the main thread!", i);
        thread::sleep(Duration::from_millis(1));
    }
}
```

当主线程结束时，新线程也会结束，而不管其是否执行完毕。

## 通过join Handle来等待所有线程的完成

- thread::spawn 的返回值类型是 JoinHandle。
- JoinHandle 是一个拥有所有权的值
- 当对其调用 join 方法时，会阻止当前运行线程的执行，直到handle所表示的这些线程的终结。

文件名: src/main.rs

```plain
use std::thread;
use std::time::Duration;

fn main() {
    let handle = thread::spawn(|| {
        for i in 1..10 {
            println!("hi number {} from the spawned thread!", i);
            thread::sleep(Duration::from_millis(1));
        }
    });

    for i in 1..5 {
        println!("hi number {} from the main thread!", i);
        thread::sleep(Duration::from_millis(1));
    }

    handle.join().unwrap();
}
```

通过调用 handle 的 join 会阻塞当前线程直到 handle 所代表的线程结束。**阻塞**（*Blocking*） 线程意味着阻止该线程执行工作或退出。因为我们将 join 调用放在了主线程的 for 循环之后，

## 使用move闭包

- move闭包通常和thread::spawn函数一起使用，它允许你使用其他线程的数据
- 创建线程时，把值得所有权从一个线程转移到另一个线程

文件名: src/main.rs

```plain
use std::thread;

fn main() {
    let v = vec![1, 2, 3];

    let handle = thread::spawn(|| {
        println!("Here's a vector: {:?}", v);
    });

    handle.join().unwrap();
}
```

示例 16-3: 尝试在另一个线程使用主线程创建的 vector

闭包使用了 v，所以闭包会捕获 v 并使其成为闭包环境的一部分。因为 thread::spawn 在一个新线程中运行这个闭包，所以可以在新线程中访问 v。然而当编译这个例子时，会得到如下错误：

```plain
$ cargo run
   Compiling threads v0.1.0 (file:///projects/threads)
error[E0373]: closure may outlive the current function, but it borrows `v`, which is owned by the current function
 --> src/main.rs:6:32
  |
6 |     let handle = thread::spawn(|| {
  |                                ^^ may outlive borrowed value `v`
7 |         println!("Here's a vector: {:?}", v);
  |                                           - `v` is borrowed here
  |
note: function requires argument type to outlive `'static`
 --> src/main.rs:6:18
  |
6 |       let handle = thread::spawn(|| {
  |  __________________^
7 | |         println!("Here's a vector: {:?}", v);
8 | |     });
  | |______^
help: to force the closure to take ownership of `v` (and any other referenced variables), use the `move` keyword
  |
6 |     let handle = thread::spawn(move || {
  |                                ++++

For more information about this error, try `rustc --explain E0373`.
error: could not compile `threads` due to previous error
```

Rust 会 **推断** 如何捕获 v，因为 println! 只需要 v 的引用，闭包尝试借用 v。然而这有一个问题：Rust 不知道这个新建线程会执行多久，所以无法知晓 v 的引用是否一直有效。

示例 16-4 展示了一个 v 的引用很有可能不再有效的场景：

文件名: src/main.rs

```plain
use std::thread;

fn main() {
    let v = vec![1, 2, 3];

    let handle = thread::spawn(|| {
        println!("Here's a vector: {:?}", v);
    });

    drop(v); // oh no!

    handle.join().unwrap();
}
```

通过在闭包之前增加 move 关键字，我们强制闭包获取其使用的值的所有权，而不是任由 Rust 推断它应该借用值。示例 16-5 中展示的对示例 16-3 代码的修改，可以按照我们的预期编译并运行：

文件名: src/main.rs

```plain
use std::thread;

fn main() {
    let v = vec![1, 2, 3];

    let handle = thread::spawn(move || {
        println!("Here's a vector: {:?}", v);
    });

    handle.join().unwrap();
}
```

示例 16-5: 使用 move 关键字强制获取它使用的值的所有权

## 使用消息传递来跨线程传递数据

一个日益流行的确保安全并发的方式是 **消息传递**（*message passing*），这里线程或 actor 通过发送包含数据的消息来相互沟通。这个思想来源于 [Go 编程语言文档中]的口号：“不要通过共享内存来通讯；而是通过通讯来共享内存。”（“Do not communicate by sharing memory; instead, share memory by communicating.”）

- 线程（或Actor）通过彼此发送消息（数据）来进行通信
- Rust: Channel(标准库提供)

### Channel

- Channel包含：发送端，接收端
- 调用发送端的方法，发送数据
- 接收端会检查和接收到达的数据
- 如果发送端，接收端中任意一端被丢弃了，那么Channel就被“关闭”了

### 创建Channel

- 使用**mpsc::channel** 函数来创建Channel

1. mpsc表示multiple producer,single consumer(多个生产者，一个消费者)
2. 返回一个tuple(元组):里面元素分别是发送端，接收端

让我们将发送端移动到一个新建线程中并发送一个字符串，这样新建线程就可以和主线程通讯了

```plain
use std::sync::mpsc;
use std::thread;

fn main() {
    let (tx, rx) = mpsc::channel();

    thread::spawn(move || {
        let val = String::from("hi");
        tx.send(val).unwrap();
    });
    let received = rx.recv().unwrap();
    println!("Got: {}", received);
}
```

示例 16-7: 将 tx 移动到一个新建的线程中并发送 “hi”

这里再次使用 thread::spawn 来创建一个新线程并使用 move 将 tx 移动到闭包中这样新建线程就拥有 tx 了。新建线程需要拥有信道的发送端以便能向信道发送消息。

信道的发送端有一个 send 方法用来获取需要放入信道的值。send 方法返回一个 Result<T, E> 类型，所以**如果接收端已经被丢弃了，将没有发送值的目标，所以发送操作会返回错误**。在这个例子中，出错的时候调用 unwrap 产生 panic。不过对于一个真实程序，需要合理地处理它

**接收端的recv方法**

- 信道的接收端有两个有用的方法：recv 和 try_recv。
- 这里，我们使用了 recv，它是 *receive* 的缩写。这个方法会**阻塞主线程执行直到从信道中接收一个值**。一旦发送了一个值，recv 会在一个 Result<T, E> 中返回它。当信道发送端关闭，recv 会返回一个错误表明不会再有新的值到来了。
- try_recv 不会阻塞，相反它立刻返回一个 Result<T, E>：Ok 值包含可用的信息，而 Err 值代表此时没有任何消息。如果线程在等待消息过程中还有其他工作时使用 try_recv 很有用：可以编写一个循环来频繁调用 try_recv，在有可用消息时进行处理，其余时候则处理一会其他工作直到再次检查。

### 信道与所有权转移

现在让我们做一个试验来看看信道与所有权如何一同协作以避免产生问题：我们将尝试在新建线程中的信道中发送完 val 值 **之后** 再使用它。尝试编译示例 16-9 中的代码并看看为何这是不允许的：

文件名: src/main.rs

```plain
use std::sync::mpsc;
use std::thread;

fn main() {
    let (tx, rx) = mpsc::channel();

    thread::spawn(move || {
        let val = String::from("hi");
        tx.send(val).unwrap();
        println!("val is {}", val);
    });

    let received = rx.recv().unwrap();
    println!("Got: {}", received);
}
```

示例 16-9: 在我们已经发送到信道中后，尝试使用 val 引用

这里尝试在通过 tx.send 发送 val 到信道中之后将其打印出来。允许这么做是一个坏主意：

**一旦将值发送到另一个线程后，那个线程可能会在我们再次使用它之前就将其修改或者丢弃。其他线程对值可能的修改会由于不一致或不存在的数据而导致错误或意外的结果**。

然而，尝试编译示例 16-9 的代码时，Rust 会给出一个错误：

```plain
$ cargo run
   Compiling message-passing v0.1.0 (file:///projects/message-passing)
error[E0382]: borrow of moved value: `val`
  --> src/main.rs:10:31
   |
8  |         let val = String::from("hi");
   |             --- move occurs because `val` has type `String`, which does not implement the `Copy` trait
9  |         tx.send(val).unwrap();
   |                 --- value moved here
10 |         println!("val is {}", val);
   |                               ^^^ value borrowed here after move

For more information about this error, try `rustc --explain E0382`.
error: could not compile `message-passing` due to previous error
```

我们的并发错误会造成一个编译时错误。send 函数获取其参数的所有权并移动这个值归接收者所有。这可以防止在发送后再次意外地使用这个值；所有权系统检查一切是否合乎规则。

### 发送多个值并观察接收者的等待

示例 16-8 中的代码可以编译和运行，不过它并没有明确的告诉我们两个独立的线程通过信道相互通讯。

示例 16-10 则有一些改进会证明示例 16-8 中的代码是并发执行的

新建线程现在会发送多个消息并在每个消息之间暂停一秒钟。

文件名: src/main.rs

```plain
use std::sync::mpsc;
use std::thread;
use std::time::Duration;

fn main() {
    let (tx, rx) = mpsc::channel();

    thread::spawn(move || {
        let vals = vec![
            String::from("hi"),
            String::from("from"),
            String::from("the"),
            String::from("thread"),
        ];

        for val in vals {
            tx.send(val).unwrap();
            thread::sleep(Duration::from_secs(1));
        }
    });

    for received in rx {
        println!("Got: {}", received);
    }
}
```

示例 16-10: 发送多个消息，并在每次发送后暂停一段时间

这一次，在新建线程中有一个字符串 vector 希望发送到主线程。我们遍历他们，单独的发送每一个字符串并通过一个 Duration 值调用 thread::sleep 函数来暂停一秒。

在主线程中，不再显式调用 recv 函数：而是将 rx 当作一个迭代器。对于每一个接收到的值，我们将其打印出来。当信道被关闭时，迭代器也将结束。

当运行示例 16-10 中的代码时，将看到如下输出，每一行都会暂停一秒：

```plain
Got: hi
Got: from
Got: the
Got: thread
```

因为主线程中的 for 循环里并没有任何暂停或等待的代码，所以可以说主线程是在等待从新建线程中接收值。

### 通过克隆发送者来创建多个生产者

文件名: src/main.rs

```plain
// --snip--

    let (tx, rx) = mpsc::channel();

    let tx1 = tx.clone();
    thread::spawn(move || {
        let vals = vec![
            String::from("hi"),
            String::from("from"),
            String::from("the"),
            String::from("thread"),
        ];

        for val in vals {
            tx1.send(val).unwrap();
            thread::sleep(Duration::from_secs(1));
        }
    });

    thread::spawn(move || {
        let vals = vec![
            String::from("more"),
            String::from("messages"),
            String::from("for"),
            String::from("you"),
        ];

        for val in vals {
            tx.send(val).unwrap();
            thread::sleep(Duration::from_secs(1));
        }
    });

    for received in rx {
        println!("Got: {}", received);
    }

    // --snip--
```

示例 16-11: 从多个生产者发送多个消息

这一次，在创建新线程之前，我们对信道的发送端调用了 clone 方法。这会给我们一个**可以传递给第一个新建线程的发送端句柄**。我们会将原始的信道发送端传递给第二个新建线程。这样就会有两个线程，每个线程将向信道的接收端发送不同的消息。

如果运行这些代码，你 **可能** 会看到这样的输出：

```plain
Got: hi
Got: more
Got: from
Got: messages
Got: for
Got: the
Got: thread
Got: you
```

虽然你可能会看到这些值以不同的顺序出现；这依赖于你的系统。这也就是并发既有趣又困难的原因。如果通过 thread::sleep 做实验，在不同的线程中提供不同的值，就会发现他们的运行更加不确定，且每次都会产生不同的输出。

## 共享状态并发

- 在某种程度上，任何编程语言中的信道都类似于单所有权，因为一旦将一个值传送到信道中，将无法再使用这个值。
- 共享内存类似于多所有权：多个线程可以同时访问相同的内存位置

### 互斥器一次只允许一个线程访问数据

**互斥器**（*mutex*）是 *mutual exclusion* 的缩写，也就是说，任意时刻，其只允许一个线程访问某些数据。为了访问互斥器中的数据，线程首先需要通过获取互斥器的 **锁**（*lock*）来表明其希望访问数据。锁是一个作为互斥器一部分的数据结构，它记录谁有数据的排他访问权。因此，我们描述互斥器为通过锁系统 **保护**（*guarding*）其数据。

互斥器以难以使用著称，因为你不得不记住：

1. 在使用数据之前尝试获取锁。
2. 处理完被互斥器所保护的数据之后，必须解锁数据，这样其他线程才能够获取锁。

在 Rust 中，得益于类型系统和所有权，我们不会在锁和解锁上出错。

### Mutex的 API

作为展示如何使用互斥器的例子，让我们从在单线程上下文使用互斥器开始，如示例 16-12 所示：

文件名: src/main.rs

```plain
use std::sync::Mutex;

fn main() {
    let m = Mutex::new(5);

    {
        let mut num = m.lock().unwrap();
        *num = 6;
    }

    println!("m = {:?}", m);
}
```

示例 16-12: 出于简单的考虑，在一个单线程上下文中探索 Mutex<T> 的 API

像很多类型一样，我们使用关联函数 new 来创建一个 Mutex<T>。使用 lock 方法获取锁，以访问互斥器中的数据。这个调用会阻塞当前线程，直到我们拥有锁为止。

一旦获取了锁，就可以将返回值（在这里是num）**视为一个其内部数据的可变引用**了。类型系统确保了我们在使用 m 中的值之前获取锁：Mutex<i32> 并不是一个 i32，所以 **必须** 获取锁才能使用这个 i32 值。我们是不会忘记这么做的，因为反之类型系统不允许访问内部的 i32 值。

Mutex<T> 是一个智能指针。更准确的说，lock 调用 **返回** 一个叫做 MutexGuard 的智能指针。这个智能指针实现了 Deref 来指向其内部数据；其也提供了一个 Drop 实现当 MutexGuard 离开作用域时自动释放锁，这正发生于示例 16-12 内部作用域的结尾。为此，我们不会忘记释放锁并阻塞互斥器为其它线程所用的风险，因为**锁的释放是自动发生的**。

### 在线程间共享 Mutex

现在让我们尝试使用 Mutex<T> 在多个线程间共享值。我们将启动十个线程，并在各个线程中对同一个计数器值加一，这样计数器将从 0 变为 10。示例 16-13 中的例子会出现编译错误，而我们将通过这些错误来学习如何使用 Mutex<T>，以及 Rust 又是如何帮助我们正确使用的。

文件名: src/main.rs

```plain
use std::sync::Mutex;
use std::thread;

fn main() {
    let counter = Mutex::new(0);
    let mut handles = vec![];

    for _ in 0..10 {
        let handle = thread::spawn(move || {
            let mut num = counter.lock().unwrap();

            *num += 1;
        });
        handles.push(handle);
    }

    for handle in handles {
        handle.join().unwrap();
    }

    println!("Result: {}", *counter.lock().unwrap());
}
```

示例 16-13: 程序启动了 10 个线程，每个线程都通过 Mutex<T> 来增加计数器的值

这里创建了一个 counter 变量来存放内含 i32 的 Mutex<T>，类似示例 16-12 那样。接下来遍历 range 创建了 10 个线程。使用了 thread::spawn 并对所有线程使用了相同的闭包：他们每一个都将调用 lock 方法来获取 Mutex<T> 上的锁，接着将互斥器中的值加一。当一个线程结束执行，num 会离开闭包作用域并释放锁，这样另一个线程就可以获取它了。

在主线程中，我们像示例 16-2 那样收集了所有的 join 句柄，调用它们的 join 方法来确保所有线程都会结束。这时，主线程会获取锁并打印出程序的结果。

编译失败：

```plain
$ cargo run
   Compiling shared-state v0.1.0 (file:///projects/shared-state)
error[E0382]: use of moved value: `counter`
  --> src/main.rs:9:36
   |
5  |     let counter = Mutex::new(0);
   |         ------- move occurs because `counter` has type `Mutex<i32>`, which does not implement the `Copy` trait
...
9  |         let handle = thread::spawn(move || {
   |                                    ^^^^^^^ value moved into closure here, in previous iteration of loop
10 |             let mut num = counter.lock().unwrap();
   |                           ------- use occurs due to use in closure

For more information about this error, try `rustc --explain E0382`.
error: could not compile `shared-state` due to previous error
```

错误信息表明 counter 值在上一次循环中被移动了。所以 Rust 告诉我们**不能将** **counter** **锁的所有权移动到多个线程中**。

### 多线程和多所有权

通过使用智能指针 Rc<T> 来创建引用计数的值，可以拥有多所有者。

文件名: src/main.rs

```plain
use std::rc::Rc;
use std::sync::Mutex;
use std::thread;

fn main() {
    let counter = Rc::new(Mutex::new(0));
    let mut handles = vec![];

    for _ in 0..10 {
        let counter = Rc::clone(&counter);
        let handle = thread::spawn(move || {
            let mut num = counter.lock().unwrap();

            *num += 1;
        });
        handles.push(handle);
    }

    for handle in handles {
        handle.join().unwrap();
    }

    println!("Result: {}", *counter.lock().unwrap());
}
```

示例 16-14: 尝试使用 Rc<T> 来允许多个线程拥有 Mutex<T>

再一次编译并...出现了不同的错误！

```plain
$ cargo run
   Compiling shared-state v0.1.0 (file:///projects/shared-state)
error[E0277]: `Rc<Mutex<i32>>` cannot be sent between threads safely
   --> src/main.rs:11:22
    |
11  |           let handle = thread::spawn(move || {
    |  ______________________^^^^^^^^^^^^^_-
    | |                      |
    | |                      `Rc<Mutex<i32>>` cannot be sent between threads safely
12  | |             let mut num = counter.lock().unwrap();
13  | |
14  | |             *num += 1;
15  | |         });
    | |_________- within this `[closure@src/main.rs:11:36: 15:10]`
    |
    = help: within `[closure@src/main.rs:11:36: 15:10]`, the trait `Send` is not implemented for `Rc<Mutex<i32>>`
    = note: required because it appears within the type `[closure@src/main.rs:11:36: 15:10]`
note: required by a bound in `spawn`

For more information about this error, try `rustc --explain E0277`.
error: could not compile `shared-state` due to previous error
```

第一行错误表明 Rc<Mutex<i32>>` cannot be sent between threads safely`。编译器也告诉了我们原因 `the trait `Send` is not implemented for `Rc<Mutex<i32>>。下一部分会讲到 Send：这是确保所使用的类型可以用于并发环境的 trait 之一。

不幸的是，**Rc<T>** **并不能安全的在线程间共享**。当 Rc<T> 管理引用计数时，它必须在每一个 clone 调用时增加计数，并在每一个克隆被丢弃时减少计数。**Rc<T>** **并没有使用任何并发原语，来确保改变计数的操作不会被其他线程打断**。在计数出错时可能会导致诡异的 bug，比如可能会造成内存泄漏，或在使用结束之前就丢弃一个值。我们所需要的是一个完全类似 Rc<T>，又以一种线程安全的方式改变引用计数的类型。

### 原子引用计数Arc<T>

Arc<T>**正是** 这么一个类似 Rc<T> 并可以安全的用于并发环境的类型。字母 “a” 代表 **原子性**（*atomic*），所以这是一个 **原子引用计数**（*atomically reference counted*）类型.

为什么不是所有的原始类型都是原子性的？为什么不是所有标准库中的类型都默认使用 Arc<T> 实现？

原因在于线程安全带有性能惩罚，我们希望只在必要时才为此买单。如果只是在单线程中对值进行操作，原子性提供的保证并无必要，代码可以因此运行的更快。

文件名: src/main.rs

```plain
use std::sync::{Arc, Mutex};
use std::thread;

fn main() {
    let counter = Arc::new(Mutex::new(0));
    let mut handles = vec![];

    for _ in 0..10 {
        let counter = Arc::clone(&counter);
        let handle = thread::spawn(move || {
            let mut num = counter.lock().unwrap();

            *num += 1;
        });
        handles.push(handle);
    }

    for handle in handles {
        handle.join().unwrap();
    }

    println!("Result: {}", *counter.lock().unwrap());
}
```

示例 16-15: 使用 Arc<T> 包装一个 Mutex<T> 能够实现在多线程之间共享所有权

这会打印出：

Result: 10

### RefCell/Rc 与 Mutex/Arc 的相似性

- 因为 counter 是不可变的，不过可以获取其内部值的可变引用；这意味着 Mutex<T> 提供了内部可变性，就像 Cell 系列类型那样。正如使用 RefCell<T> 可以改变 Rc<T> 中的内容那样，同样的可以使用 Mutex<T> 来改变 Arc<T> 中的内容。
- Rust 不能避免使用 Mutex<T> 的全部逻辑错误。回忆一下使用 Rc<T> 就有造成引用循环的风险，这时两个 Rc<T> 值相互引用，造成内存泄漏。同理，Mutex<T> 也有造成 **死锁**（*deadlock*） 的风险。这发生于当一个操作需要锁住两个资源而两个线程各持一个锁，这会造成它们永远相互等待。

## 使用Sync和Send trait的可扩展并发

Rust 的并发模型中一个有趣的方面是：语言本身对并发知之 **甚少**。我们之前讨论的几乎所有内容，都属于标准库，而不是语言本身的内容。由于不需要语言提供并发相关的基础设施，并发方案不受标准库或语言所限：我们可以编写自己的或使用别人编写的并发功能。

然而有两个并发概念是**内嵌于语言中**的：std::marker 中的 Sync 和 Send trait。

### 通过Send允许在线程间转移所有权

- Send 标记 trait 表明实现了 Send 的类型值的所有权可以在线程间传送。
- 几乎所有的 Rust 类型都是Send 的，
- 不过有一些例外，包括 Rc<T>：这是不能 Send 的，

因为如果克隆了 Rc<T> 的值并尝试将克隆的所有权转移到另一个线程，这两个线程都可能同时更新引用计数。为此，Rc<T> 被实现为用于单线程场景，这时不需要为拥有线程安全的引用计数而付出性能代价。

- Rust 类型系统和 trait bound 确保永远也不会意外的将不安全的 Rc<T> 在线程间发送。当尝试在示例 16-14 中这么做的时候，会得到错误 the trait Send is not implemented for Rc<Mutex<i32>>。而使用标记为 Send 的 Arc<T> 时，就没有问题了。
- 任何完全由 Send 的类型组成的类型也会自动被标记为 Send。几乎所有基本类型都是 Send 的，除了裸指针（raw pointer）。

### Sync允许多线程访问

- Sync 标记 trait 表明一个实现了 Sync 的类型可以**安全的在多个线程中拥有其值的引用**。
- 换一种方式来说，对于任意类型 T，如果 &T（T 的不可变引用）是 Send 的话 T 就是 Sync 的，这意味着其引用就可以安全的发送到另一个线程。
- 类似于 Send 的情况，基本类型是 Sync 的，完全由 Sync 的类型组成的类型也是 Sync 的。
- 智能指针 Rc<T> 也不是 Sync 的，出于其不是 Send 相同的原因。RefCell<T>和 Cell<T> 系列类型不是 Sync 的。RefCell<T> 在运行时所进行的借用检查也不是线程安全的。
- Mutex<T> 是 Sync 的，正如 “在线程间共享 Mutex”部分所讲的它可以被用来在多线程中共享访问。

### 手动实现Send和Sync是不安全的

- 通常并不需要手动实现 Send 和 Sync trait，因为由 Send 和 Sync 的类型组成的类型，自动就是 Send 和 Sync 的。
- 因为他们是标记 trait，甚至都不需要实现任何方法。他们只是用来加强并发相关的不可变性的。
- 手动实现这些标记 trait 涉及到编写不安全的 Rust 代码，

当前重要的是，在创建新的由不是 Send 和 Sync 的部分构成的并发类型时需要多加小心，以确保维持其安全保证。[“The Rustonomicon”](https://doc.rust-lang.org/nomicon/index.html) 中有更多关于这些保证以及如何维持他们的信息。

# 二十一.Rust的面向对象特性

## 面向对象语言的特点

- **对象包含数据和行为**

在这个定义下，Rust 是面向对象的：结构体和枚举包含数据而 impl 块提供了在结构体和枚举之上的方法。虽然带有方法的结构体和枚举并不被 **称为** 对象，但是他们提供了与对象相同的功能，

- **封装隐藏了实现细节**

**封装**（*encapsulation*）的思想：对象的实现细节不能被使用对象的代码获取到。所以唯一与对象交互的方式是通过对象提供的公有 API；使用对象的代码无法深入到对象内部并直接改变数据或者行为。封装使得改变和重构对象的内部时无需改变使用对象的代码。

Rust中可以使用 pub 关键字来决定模块、类型、函数和方法是公有的，而默认情况下其他一切都是私有的。

举例：

比如，我们可以定义一个包含一个 i32 类型 vector 的结构体 AveragedCollection。结构体也可以有一个字段，该字段保存了 vector 中所有值的平均值。这样，希望知道结构体中的 vector 的平均值的人可以随时获取它，而无需自己计算。换句话说，AveragedCollection 会为我们缓存平均值结果。示例 17-1 有 AveragedCollection 结构体的定义：

文件名: src/lib.rs

```plain
pub struct AveragedCollection {
    list: Vec<i32>,
    average: f64,
}
```

示例 17-1: AveragedCollection 结构体维护了一个整型列表和集合中所有元素的平均值。

注意，结构体自身被标记为 pub，这样其他代码就可以使用这个结构体，但是在结构体内部的字段仍然是私有的。这是非常重要的，因为我们希望保证变量被增加到列表或者被从列表删除时，也会同时更新平均值。可以通过在结构体上实现 add、remove 和 average 方法来做到这一点，如示例 17-2 所示：

文件名: src/lib.rs

```plain
impl AveragedCollection {
    pub fn add(&mut self, value: i32) {
        self.list.push(value);
        self.update_average();
    }

    pub fn remove(&mut self) -> Option<i32> {
        let result = self.list.pop();
        match result {
            Some(value) => {
                self.update_average();
                Some(value)
            }
            None => None,
        }
    }

    pub fn average(&self) -> f64 {
        self.average
    }

    fn update_average(&mut self) {
        let total: i32 = self.list.iter().sum();
        self.average = total as f64 / self.list.len() as f64;
    }
}
```

示例 17-2: 在AveragedCollection 结构体上实现了add、remove 和 average 公有方法

公有方法 add、remove 和 average 是修改 AveragedCollection 实例的唯一方式。当使用 add 方法把一个元素加入到 list 或者使用 remove 方法来删除时，这些方法的实现同时会调用私有的 update_average 方法来更新 average 字段。

list 和 average 是私有的，所以没有其他方式来使得外部的代码直接向 list 增加或者删除元素，否则 list 改变时可能会导致 average 字段不同步。average 方法返回 average 字段的值，这使得外部的代码只能读取 average 而不能修改它。

因为我们已经封装好了 AveragedCollection 的实现细节，将来可以轻松改变类似数据结构这些方面的内容。例如，可以使用 HashSet<i32> 代替 Vec<i32> 作为 list 字段的类型。只要 add、remove 和 average 公有函数的签名保持不变，使用 AveragedCollection 的代码就无需改变。相反如果使得 list 为公有，就未必都会如此了： HashSet<i32> 和 Vec<i32> 使用不同的方法增加或移除项，所以如果要想直接修改 list 的话，外部的代码可能不得不做出修改。

如果封装是一个语言被认为是面向对象语言所必要的方面的话，那么 Rust 满足这个要求。在代码中不同的部分使用 pub 与否可以封装其实现细节。

- **继承，作为类型系统与代码共享**

**继承**（*Inheritance*）是一个很多编程语言都提供的机制，一个对象可以定义为继承另一个对象的定义，这使其可以获得父对象的数据和行为，而无需重新定义。

**如果一个语言必须有继承才能被称为面向对象语言的话，那么 Rust 就不是面向对象的。****无法定义一个结构体继承父结构体的成员和方法**。然而，如果你过去常常在你的编程工具箱使用继承，根据你最初考虑继承的原因，Rust 也提供了其他的解决方案。

选择继承有两个主要的原因。

1. 第一个是为了重用代码：一旦为一个类型实现了特定行为，继承可以对一个不同的类型重用这个实现。相反 Rust 代码可以使用默认 trait 方法实现来进行共享，
2. 第二个使用继承的原因与类型系统有关：表现为子类型可以用于父类型被使用的地方。这也被称为 **多态**（*polymorphism*），这意味着如果多种对象共享特定的属性，则可以相互替代使用。

近来继承作为一种语言设计的解决方案在很多语言中失宠了，因为其时常带有共享多于所需的代码的风险。子类不应总是共享其父类的所有特征，但是继承却始终如此。如此会使程序设计更为不灵活，并引入无意义的子类方法调用，或由于方法实际并不适用于子类而造成错误的可能性。某些语言还只允许子类继承一个父类，进一步限制了程序设计的灵活性。

## 顾及不同类型值得trait对象

vector 只能存储同种类型元素的局限。示例 8-10 中提供了一个定义 SpreadsheetCell 枚举来储存整型，浮点型和文本成员的替代方案。这意味着可以在每个单元中储存不同类型的数据，并仍能拥有一个代表一排单元的 vector。这在当编译代码时就知道希望可以交替使用的类型为固定集合的情况下是完全可行的。

**然而有时我们希望库用户在特定情况下能够扩展有效的类型集合。**

为了展示如何实现这一点，这里将创建一个图形用户接口（Graphical User Interface， GUI）工具的例子，它通过遍历列表并调用每一个项目的 draw 方法来将其绘制到屏幕上 —— 此乃一个 GUI 工具的常见技术。我们将要创建一个叫做 gui 的库 crate，它含一个 GUI 库的结构。这个 GUI 库包含一些可供开发者使用的类型，比如 Button 或 TextField。在此之上，gui 的用户希望创建自定义的可以绘制于屏幕上的类型：比如，一个程序员可能会增加 Image，另一个可能会增加 SelectBox。

这个例子中并不会实现一个功能完善的 GUI 库，不过会展示其中各个部分是如何结合在一起的。编写库的时候，我们不可能知晓并定义所有其他程序员希望创建的类型。我们所知晓的是 gui 需要记录一系列不同类型的值，并需要能够对其中每一个值调用 draw 方法。这里无需知道调用 draw 方法时具体会发生什么，只要该值会有那个方法可供我们调用。

在拥有继承的语言中，可以定义一个名为 Component 的类，该类上有一个 draw 方法。其他的类比如 Button、Image 和 SelectBox 会从 Component 派生并因此继承 draw 方法。它们各自都可以覆盖 draw 方法来定义自己的行为，但是框架会把所有这些类型当作是 Component 的实例，并在其上调用 draw。

不过 Rust 并没有继承，我们得另寻出路。

## 定义通用行为的trait

为了实现 gui 所期望的行为，让我们定义一个 Draw trait，其中包含名为 draw 的方法。接着可以定义一个存放 **trait 对象**（***trait object\***） 的 vector。trait 对象指向一个实现了我们指定 trait 的类型的实例，以及一个用于在运行时查找该类型的trait方法的表。我们通过指定某种指针来创建 trait 对象，例如 & 引用或 Box<T> 智能指针，还有 dyn keyword， 以及指定相关的 trait（ [“动态大小类型和 Sized trait”] 部分会介绍 trait 对象必须使用指针的原因）。我们可以使用 trait 对象代替泛型或具体类型。任何使用 trait 对象的位置，Rust 的类型系统会在编译时确保任何在此上下文中使用的值会实现其 trait 对象的 trait。如此便无需在编译时就知晓所有可能的类型。

Rust 刻意不将结构体与枚举称为 “对象”，以便与其他语言中的对象相区别。在结构体或枚举中，结构体字段中的数据和 impl 块中的行为是分开的，不同于其他语言中将数据和行为组合进一个称为对象的概念中。

trait 对象将数据和行为两者相结合，从这种意义上说 **则** 其更类似其他语言中的对象。不过 trait 对象不同于传统的对象，因为不能向 trait 对象增加数据。trait 对象并不像其他语言中的对象那么通用：其（trait 对象）具体的作用是允许对通用行为进行抽象。

示例 17-3 展示了如何定义一个带有 draw 方法的 trait Draw：

文件名: src/lib.rs

```plain
pub trait Draw {
    fn draw(&self);
}
```

示例 17-3：Draw trait 的定义

示例 17-4 定义了一个存放了名叫 components 的 vector 的结构体 Screen。这个 vector 的类型是 Box<dyn Draw>，此为一个 trait 对象：它是 Box 中任何实现了 Draw trait 的类型的替身。

文件名: src/lib.rs

```plain
pub struct Screen {
    pub components: Vec<Box<dyn Draw>>,
}
```

示例 17-4: 一个 Screen 结构体的定义，它带有一个字段 components，其包含实现了 Draw trait 的 trait 对象的 vector

在 Screen 结构体上，我们将定义一个 run 方法，该方法会对其 components 上的每一个组件调用 draw 方法，如示例 17-5 所示：

文件名: src/lib.rs

```plain
impl Screen {
    pub fn run(&self) {
        for component in self.components.iter() {
            component.draw();
        }
    }
}
```

示例 17-5：在 Screen 上实现一个 run 方法，该方法在每个 component 上调用 draw 方法

这与定义使用了带有 trait bound 的泛型类型参数的结构体不同。**泛型类型参数一次只能替代一个具体类型**，而 **trait 对象则允许在运行时替代多种具体类型**。例如，可以定义 Screen 结构体来使用泛型和 trait bound，如示例 17-6 所示：

文件名: src/lib.rs

```plain
pub trait Draw {
    fn draw(&self);
}
pub struct Screen<T: Draw> {
    pub components: Vec<T>,
}

impl<T> Screen<T>
where
    T: Draw,
{
    pub fn run(&self) {
        for component in self.components.iter() {
            component.draw();
        }
    }
}
```

示例 17-6: 一种 Screen 结构体的替代实现，其 run 方法使用泛型和 trait bound

这**限制了**Screen 实例必须拥有一个全是 Button 类型或者全是 TextField 类型的组件列表。如果只需要**同质（相同类型）集合**，则倾向于使用泛型和 trait bound，因为其定义会在编译时采用具体类型进行单态化。

```plain
pub trait Draw {
    fn draw(&self);
}
pub struct Screen {
    pub components: Vec<Box<dyn Draw>>,
}
impl Screen {
    pub fn run(&self) {
        for component in self.components.iter() {
            component.draw();
        }
    }
}
```

另一方面，通过使用 trait 对象的方法，一个 Screen 实例可以存放一个既能包含 Box<Button>，也能包含 Box<TextField> 的 Vec<T>。

**实现trait**

现在来增加一些实现了 Draw trait 的类型。我们将提供 Button 类型。再一次重申，真正实现 GUI 库超出了范畴，所以 draw 方法体中不会有任何有意义的实现。为了想象一下这个实现看起来像什么，一个 Button 结构体可能会拥有 width、height 和 label 字段，如示例 17-7 所示：

文件名: src/lib.rs

```plain
pub struct Button {
    pub width: u32,
    pub height: u32,
    pub label: String,
}

impl Draw for Button {
    fn draw(&self) {
        // code to actually draw a button
    }
}
```

示例 17-7: 一个实现了 Draw trait 的 Button 结构体

在 Button 上的 width、height 和 label 字段会和其他组件不同，比如 TextField 可能有 width、height、label 以及 placeholder 字段。每一个我们希望能在屏幕上绘制的类型都会使用不同的代码来实现 Draw trait 的 draw 方法来定义如何绘制特定的类型，像这里的 Button 类型（并不包含任何实际的 GUI 代码，这超出了本章的范畴）。除了实现 Draw trait 之外，比如 Button 还可能有另一个包含按钮点击如何响应的方法的 impl 块。这类方法并不适用于像 TextField 这样的类型。

如果一些库的使用者决定实现一个包含 width、height 和 options 字段的结构体 SelectBox，并且也为其实现了 Draw trait，如示例 17-8 所示：

文件名: src/main.rs

```plain
use gui::Draw;

struct SelectBox {
    width: u32,
    height: u32,
    options: Vec<String>,
}

impl Draw for SelectBox {
    fn draw(&self) {
        // code to actually draw a select box
    }
}
```

示例 17-8: 另一个使用 gui 的 crate 中，在 SelectBox 结构体上实现 Draw trait

库使用者现在可以在他们的 main 函数中创建一个 Screen 实例。至此可以通过将 SelectBox 和 Button 放入 Box<T> 转变为 trait 对象来增加组件。接着可以调用 Screen 的 run 方法，它会调用每个组件的 draw 方法。示例 17-9 展示了这个实现：

文件名: src/main.rs

```plain
use gui::{Button, Screen};

fn main() {
    let screen = Screen {
        components: vec![
            Box::new(SelectBox {
                width: 75,
                height: 10,
                options: vec![
                    String::from("Yes"),
                    String::from("Maybe"),
                    String::from("No"),
                ],
            }),
            Box::new(Button {
                width: 50,
                height: 10,
                label: String::from("OK"),
            }),
        ],
    };

    screen.run();
}
```

示例 17-9: 使用 trait 对象来存储实现了相同 trait 的不同类型的值

当编写库的时候，我们不知道何人会在何时增加 SelectBox 类型，不过 Screen 的实现能够操作并绘制这个新类型，因为 SelectBox 实现了 Draw trait，这意味着它实现了 draw 方法。

这个概念 —— 只关心值所反映的信息而不是其具体类型 —— 类似于动态类型语言中称为 **鸭子类型**（*duck typing*）的概念：如果它走起来像一只鸭子，叫起来像一只鸭子，那么它就是一只鸭子！在示例 17-5 中 Screen 上的 run 实现中，run 并不需要知道各个组件的具体类型是什么。**它并不检查组件是** **Button** **或者** **SelectBox** **的实例**。**通过指定** **Box<dyn Draw>** **作为** **components** **vector 中值的类型**，我们就定义了 Screen 为需要可以在其上调用 draw 方法的值。

使用 trait 对象和 Rust 类型系统来进行类似鸭子类型操作的优势是无需在运行时检查一个值是否实现了特定方法或者担心在调用时因为值没有实现方法而产生错误。如果值没有实现 trait 对象所需的 trait 则 Rust 不会编译这些代码。

例如，示例 17-10 展示了当创建一个使用 String 做为其组件的 Screen 时发生的情况：

文件名: src/main.rs

```plain
use gui::Screen;

fn main() {
    let screen = Screen {
        components: vec![Box::new(String::from("Hi"))],
    };

    screen.run();
}
```

示例 17-10: 尝试使用一种没有实现 trait 对象的 trait 的类型

我们会遇到这个错误，因为 String 没有实现 rust_gui::Draw trait：

```plain
$ cargo run
   Compiling gui v0.1.0 (file:///projects/gui)
error[E0277]: the trait bound `String: Draw` is not satisfied
 --> src/main.rs:5:26
  |
5 |         components: vec![Box::new(String::from("Hi"))],
  |                          ^^^^^^^^^^^^^^^^^^^^^^^^^^^^ the trait `Draw` is not implemented for `String`
  |
  = note: required for the cast to the object type `dyn Draw`

For more information about this error, try `rustc --explain E0277`.
error: could not compile `gui` due to previous error
```

这告诉了我们，要么是我们传递了并不希望传递给 Screen 的类型并应该提供其他类型，要么应该在 String 上实现 Draw 以便 Screen 可以调用其上的 draw。

## trait对象执行动态分发

- 对泛型使用 trait bound 时编译器所执行的单态化处理：编译器为每一个被泛型类型参数代替的具体类型生成了函数和方法的非泛型实现。单态化产生的代码在执行 **静态分发**（*static dispatch*）。
- 静态分发发生于编译器在编译时就知晓调用了什么方法的时候。
- **动态分发** （*dynamic dispatch* 编译器在编译时无法知晓调用了什么方法。
- 在动态分发的场景下，编译器生成的代码到运行时才能确定调用了什么方法。

**当使用 trait 对象时，Rust 必须使用动态分发**。编译器无法知晓所有可能用于 trait 对象代码的类型，所以它也不知道应该调用哪个类型的哪个方法实现。为此，Rust 在运行时使用 trait 对象中的指针来知晓需要调用哪个方法。**动态分发也阻止编译器有选择的内联方法代码**，这会相应的**禁用一些优化**。尽管在编写示例 17-5 和可以支持示例 17-9 中的代码的过程中确实获得了额外的灵活性，但仍然需要权衡取舍。

## trait对象需要类型安全

**只有对象安全（object-safe）的trait可以实现为 特征(dyn)对象** 。

这里有一些复杂的规则来实现trait的对象安全，但在实践中，只有两个相关的规则。

**如果一个 trait 中定义的所有方法都符合以下规则，则该 trait 是对象安全的：**

- 返回值不是 Self
- 没有泛型类型的参数

Self 关键字是我们在 trait 与方法上的实现的别称，trait 对象必须是对象安全的，因为一旦使用 trait 对象，Rust 将不再知晓该实现的返回类型。如果一个 trait 的方法返回了一个 Self 类型，但是该 trait 对象忘记了 Self 的确切类型，那么该方法将不能使用原本的类型。当 trait 使用具体类型填充的泛型类型时也一样：具体类型成为实现 trait 的对象的一部分，当使用 trait 对象却忘了类型是什么时，无法知道应该用什么类型来填充泛型类型。

**一个非对象安全的 trait 例子是标准库中的** **Clone** **trait**。Clone trait 中的 clone 方法的声明如下：

```plain
pub trait Clone {
    fn clone(&self) -> Self;
}
```

String 类型实现了 Clone trait，当我们在 String 的实例对象上调用 clone 方法时，我们会得到一个 String 类型实例对象。相似地，如果我们调用 Vec<T> 实例对象上的 clone 方法，我们会得到一个 Vec<T> 类型的实例对象。clone 方法的标签需要知道哪个类型是 Self 类型，因为 Self 是它的返回类型。

当我们尝试编译一些违反 trait 对象的对象安全规则的代码时，我们会收到编译器的提示。例如，我们想实现17-4的 Screen 结构体来保存一个实现了 Clone trait 而不是 Draw trait 的类型，如下所示

```plain
pub struct Screen {
    pub components: Vec<Box<dyn Clone>>,
}
```

我们将会收到如下错误：

```plain
$ cargo build
   Compiling gui v0.1.0 (file:///projects/gui)
error[E0038]: the trait `Clone` cannot be made into an object
 --> src/lib.rs:2:29
  |
2 |     pub components: Vec<Box<dyn Clone>>,
  |                             ^^^^^^^^^ `Clone` cannot be made into an object
  |
  = note: the trait cannot be made into an object because it requires `Self: Sized`
  = note: for a trait to be "object safe" it needs to allow building a vtable to allow the call to be resolvable dynamically; for more information visit <https://doc.rust-lang.org/reference/items/traits.html#object-safety>

For more information about this error, try `rustc --explain E0038`.
error: could not compile `gui` due to previous error
```

这个错误意味着我们不能将此 trait 用于 trait 对象。

```plain
// 使用至少两种方法让代码工作
// 不要添加/删除任何代码行
trait MyTrait {
    fn f(&self) -> Self;
}

impl MyTrait for u32 {
    fn f(&self) -> Self { 42 }
}

impl MyTrait for String {
    fn f(&self) -> Self { self.clone() }
}

fn my_function(x: Box<dyn MyTrait>)  {
    x.f()
}

fn main() {
    my_function(Box::new(13_u32));
    my_function(Box::new(String::from("abc")));

    println!("Success!")
}
```

第一种方式修改

```plain
trait MyTrait {
    fn f(&self) -> Self;
}

impl MyTrait for u32 {
    fn f(&self) -> u32 { 42 }
}

impl MyTrait for String {
    fn f(&self) -> String { self.clone() }
}

fn my_function(x: impl MyTrait) -> impl MyTrait  {
    x.f()
}

fn main() {
    my_function(13_u32);
    my_function(String::from("abc"));
}
```

第二种方式修改

```plain
trait MyTrait {
    fn f(&self) -> Box<dyn MyTrait>;
}

impl MyTrait for u32 {
    fn f(&self) -> Box<dyn MyTrait> { Box::new(42) }
}

impl MyTrait for String {
    fn f(&self) -> Box<dyn MyTrait> { Box::new(self.clone()) }
}

fn my_function(x: Box<dyn MyTrait>) -> Box<dyn MyTrait> {
    x.f()
}

fn main() {
    my_function(Box::new(13_u32));
    my_function(Box::new(String::from("abc")));
}
```

## 面向对象设计模式的实现

- **状态模式**（*state pattern*）是一个面向对象设计模式。该模式的关键在于一个值有某些内部状态，体现为一系列的 **状态对象**，同时值的行为随着其内部状态而改变
- 状态对象共享功能：在 Rust 中使用结构体和 trait 而不是对象和继承。每一个状态对象负责其自身的行为，以及该状态何时应当转移至另一个状态。持有一个状态对象的值对于不同状态的行为以及何时状态转移毫不知情。
- 使用状态模式意味着当程序的业务需求改变时，无需改变值持有状态或者使用值的代码。我们只需更新某个状态对象中的代码来改变其规则，或者是增加更多的状态对象。

[https://kaisery.github.io/trpl-zh-cn/ch17-03-oo-design-patterns.html#%E9%9D%A2%E5%90%91%E5%AF%B9%E8%B1%A1%E8%AE%BE%E8%AE%A1%E6%A8%A1%E5%BC%8F%E7%9A%84%E5%AE%9E%E7%8E%B0](https://kaisery.github.io/trpl-zh-cn/ch17-03-oo-design-patterns.html#面向对象设计模式的实现)

# 二十二.模式与模式匹配

**模式：**

- 模式是Rust中的一种特殊语法，用于匹配复杂和简单类型的结构
- 将模式与匹配表达式和其它构造结合使用，可以更好地控制程序的控制流
- 模式由以下元素(的一些组合)组成：

—— 字面值

—— 解构的数组，enum,struct和tuple

—— 变量

—— 通配符

—— 占位符

## match的Arm

- match VALUE{PARTTERN=>EXPRESSION,PARTTERN=>EXPRESSION,PARTTERN=>EXPRESSION,}
- 表达式的要求：

—— 详尽（包含所有的可能性）

- 一个特殊的模式：_ (下划线)—— 它不会匹配任何东西—— 不会绑定到变量—— 通常用于match的最后一个arm，或用于忽略某些值

## 条件if let表达式

- if let表达式主要是作为一种简短的方式来等价的替代只有一个匹配项的match
- if let可选的可以拥有else，包括：

—— else if

—— else if let

- 但，if let不会检查穷举性例子：

```plain
fn main() {
    let favorite_color: Option<&str> = None;
    let is_tuesday = false;
    let age: Result<u8,_> = "34".parse();

    if let Some(color) = favorite_color{
        print!("Using your favorite color,{},as the background",color);
    } else if is_tuesday{
        println!("Tuesday is green day!");
    } else if let Ok(age) = age {
        if age > 30 {
            println!("Using purple as the background color");
        } else {
            println!("Using orange as the background color");
        }
    } else {
        println!("Using blue as the background color");
    }
}
```



## while let 条件循环

- 只要模式继续满足匹配的条件，那它允许while循环一直运行

```plain
fn main() {
    let mut stack = Vec::new();
    stack.push(1);
    stack.push(2);
    stack.push(3);

    while let Some(top) = stack.pop() {
        println!("{}",top);
    }
}
```

## for循环

- for 循环是Rust中最常见的循环
- for循环中，模式就是紧随for关键字后的值

```plain
fn main() {
   let v = vec!['a','b','c'];
   for (index,value) in v.iter().enumerate(){
        println!("{} is at index {}",value,index);
   }
}
```

iter().enumerate()返回的是一个元组

## let语句

- let语句也是模式
- let PARTTERN = EXPRESSION

```plain
let a = 5;
let (x,y,z) = (1,2,3);
```



## 函数参数

- 函数的参数也可以是模式

```plain
fn foo(x: i32){
    println!("function foo");
}
fn print_coordinates(&(x,y): &(i32,i32)) {
    println!("Current location: ({},{})",x,y);
}
fn main() {
    let point = (3,5);
    print_coordinates(&point);
}
```

## 可辨驳性：模式是否会无法匹配

- 模式的两种形式：可辨驳的，无可辩驳的
- 能匹配任何可能传值的模式：无可辩驳的 入let x= 4;
- 对某些可能的值，无法进行匹配的模式:可辨驳的 例如：if let Some(x) = a_value
- **函数参数，let语句，for循环只接受无可辩驳的模式**
- **if let和while let接受可辨驳和无可辩驳的模式**

```plain
fn main() {
    let a: Option<i32> = Some(5);
    let Some(x) = a;
}
```

输出

```plain
error[E0005]: refutable pattern in local binding: `None` not covered
   --> src\main.rs:3:9
    |
3   |     let Some(x) = a;
    |         ^^^^^^^ pattern `None` not covered
    |
    = note: `let` bindings require an "irrefutable pattern", like a `struct` or an `enum` with only one variant
    = note: for more information, visit https://doc.rust-lang.org/book/ch18-02-refutability.html
```

无法匹配，因为模式没有覆盖None这种情况

修改

```plain
fn main() {
    let a: Option<i32> = Some(5);
    if let Some(x) = a{
        
    };
}
```

match除了最后一个分支，其它的分支都是可辨驳的，最后一个分支是不可辩驳的，因为它需要匹配所有剩余的情况

## 匹配字面值

```plain
fn main(){
    let x = 1;
    match x {
        1 => println!("one"),
        2 => println!("two"),
        3 => println!("three"),
        _ => println!("anything"),
    }
}
```

## 匹配命名变量

- 命名的变量是可匹配任何值的无可辩驳模式

```plain
fn main(){
    let x = Some(5);
    let y = 10;
    match x {
        Some(50) => println!("Got 50"),
        Some(y) => println!("Matched,y={:?}",y),
        _ => println!("Default Case,x={:?}",x),
    }
    println!("at the end: x={:?},y={:?}",x,y);
}
```

这里match的第二个arm中**y是一个新的变量**，存在于该arm的作用域

## 匹配一个可变引用

使用模式 &mut V 去匹配一个可变引用时，你需要格外小心，因为匹配出来的 V**是一个值**，而**不是可变引用**

```plain
fn main() {
    let mut v = String::from("hello,");
    let r = &mut v;

    match r {
        // The type of value is &mut String
       value => value.push_str(" world!") 
    }
}
```

## 多重模式

- 在match表达式中，使用 | 语法（就是或的意思）可以匹配多种模式

```plain
fn main(){
    let x= 1;
    match x {
        1 | 2 => println!("one or two"),
        3 => println!("three"),
        _ => println!("anything"),
    }
}
```

## 使用..=来匹配某个范围的值

```plain
fn main(){
    let x= 5;
    match x{
        1..=5 => println!("one through five"),
        _ => println!("something else"),
    }
    let x = 'c';
    match x {
        'a'..='j' => println!("early ASCII letter"),
        'k'..='z' => println!("late ASCII letter"),
        _ => println!("something else"),
    }
}
```

数字或字符都可以

## 解构以分解值

- 可以使用模式来结构struct,enum,tuple，从而引用这些类型值的不同部分

### 解构赋值

```plain
fn main() {
    let (x, y);
    (x,..) = (3, 4);
    [.., y] = [1, 2];
    assert_eq!([x,y],[3,2]);
}
```

### 解构元组

```plain
fn main() {
    let (mut x, y) = (1, 2);
    x += 2;

    assert_eq!(x, 3);
    assert_eq!(y, 2);
}
```

### 解构结构体

```plain
#[derive(Debug)]
struct Point{
    x: i32,
    y: i32,
}
fn main(){
    let p = Point{x:0,y:7};
    let Point {x:a,y:b}=p;
    assert_eq!(0,a);
    assert_eq!(7,b);

    //简写形式
    let Point{x,y} = p;
    assert_eq!(0,x);
    assert_eq!(7,y);

    match p {
        Point {x,y:0}=> println!("On the x axis at {}",x),//要求y必须为0
        Point {x:0,y} => println!("On the y axis at {}",y),//要求x必须为0
        Point {x,y} => println!("On neither axis:({},{})",x,y),
    }
}
```

### 解构枚举

```plain
enum Message{
    Quit,
    Move{x:i32,y:i32},
    Write(String),
    ChangeColor(i32,i32,i32),
}
fn main(){
    let msg = Message::ChangeColor(0, 160, 255);
    match msg {
        Message::Quit => {
            println!("The Quit variant has no data to destructure");
        }
        Message::Move { x, y }=>{
            println!("Move in the x direction {} and in the y direction {}",x,y);
        }
        Message::Write(text)=>{
            println!("Text message:{}",text);
        }
        Message::ChangeColor(r, g, b)=>{
            println!("rgb is ({},{},{})",r,g,b);
        }
    }
}
```

### 解构嵌套的结构体和枚举

```plain
enum Color{
    Rgb(i32,i32,i32),
    Hsv(i32,i32,i32),
}
enum Message{
    Quit,
    Move{x:i32,y:i32},
    Write(String),
    ChangeColor(Color),
}
fn main(){
    let msg = Message::ChangeColor(Color::Hsv(0, 160, 255));
    match msg {
        Message::ChangeColor(Color::Rgb(r, g, b))=>{
            println!("Change the color to red {},green {},and blue {}",r,g,b);
        }
        Message::ChangeColor(Color::Hsv(h, s, v))=>{
            println!("Change the color to hue {},saturation {},and value {}",h,s,v);
        }
        _ => (),
    }
}
```

### 解构结构体和元组

```plain
struct Point{
    x: i32,
    y: i32,
}
fn main(){
    let ((feet,inches),Point{x,y}) = ((3,10),Point{x:3,y:-10});
}
```

## 在模式中忽略值

- 有几种方式可以在模式中忽略整个值或部分值:

### **_ 忽略整个值**

```plain
fn foo(_:i32,y:i32){
    println!("y is {}",y);
}
fn main(){
    foo(3, 4);
}
```

### **使用嵌套_忽略值的一部分**

```plain
fn main(){
    let mut setting_value = Some(5);
    let new_setting_value = Some(10);
    match (setting_value,new_setting_value) {
        (Some(_),Some(_))=>{
            println!("Can't overwrite an existing customized value");
        }
        _ =>{
            setting_value = new_setting_value;
        }
    }
    println!("setting is {:?}",setting_value);

    let numbers = (3,4,8,16,32);
    match numbers {
        (first,_,third,_,fifth)=>{
            println!("Some numbers:{},{},{}",first,third,fifth);
        }
    }
}
```

### **使用以_开头命名来忽略未使用的变量**

```plain
fn main(){
    let _x = 5;
    let y = 10;//编译器警告未使用的变量

}
fn main(){
    let s = Some(String::from("Hello!"));
    if let Some(_s) = s {
        println!("found a string");
    }
    println!("{:?}",s);
}
```

模式匹配中_s是一个新的变量，模式匹配把s所有权移动到_s,后面再访问s就会报错

```plain
fn main(){
    let s = Some(String::from("Hello!"));
    if let Some(_) = s {
        println!("found a string");
    }
    println!("{:?}",s);
}
```

_ ，不会发生绑定，不会移动所有权

### **..(忽略值的剩余部分)**

```plain
struct Point{
    x: i32,
    y: i32,
    z: i32,
}
fn main(){
    let origin = Point{x:0,y:0,z:0};
    match origin {
        Point {x,..} => println!("x is {}",x),
    }
    
    let numbers = (2,4,8,16,32);
    match numbers {
        (first,..,last)=>{
            println!("Some numbers: {},{}",first,last)
        }
    }
}
```

要加逗号,

```plain
fn main() {
    let numbers = (2, 4, 8, 16, 32, 64, 128, 256, 512, 1024, 2048);

    match numbers {
        (first,..,last) => {
           assert_eq!(first, 2);
           assert_eq!(last, 2048);
        }
    }
}
```

## 使用match守卫来提供额外的条件

- match 守卫就是match arm模式后额外的if 条件，想要匹配该条件也必须满足
- match 守卫适合更复杂的场景

```plain
fn main(){
    let num = Some(4);
    match num {
        Some(x) if x < 5 => println!("less than five:{}",x),
        Some(x) => println!("{}",x),
        None => (),
    }
}
fn main(){
    let x = Some(5);
    let y = 10;
    
    match x {
        Some(50) => println!("Got 50"),
        Some(n) if n==y => println!("Matched,n = {:?}",n),//这里if n==y不是一个模式，不会引用新的变量
        _ =>println!("Default case,x ={:?}",x),
    }
    println!("at the end:x={:?},y={:?}",x,y);
}
fn main(){
    let x= 4;
    let y = false;
    match x {
        4 | 5 | 6 if y=> println!("yes"),
        _ => println!("no"),
    }
}
```

## @绑定

- @ 符号可以让我们可以创建一个变量，该变量可以在测试某个值是否与模式匹配的同时保存该值

就相当于一个等号

```plain
enum Message{
    Hello {id:i32},
}
fn main(){
    let msg = Message::Hello { id: 5 };
    match msg {
        Message::Hello { id: id_variable @ 3..=7, }=>{
            println!("Found an id in range:{}",id_variable);
        }
        Message::Hello { id: 10..=12 }=>{
            println!("Found an id in another range");
        }
        Message::Hello { id }=>{
            println!("Found some other id:{}",id);
        }
    }
}
struct Point {
    x: i32,
    y: i32,
}

fn main() {
    // fill in the blank to let p match the second arm
    let p = Point { x: 2, y: 20 }; // x can be [0, 5], y can be 10 20 or 30

    match p {
        Point { x, y: 0 } => println!("On the x axis at {}", x),
        // second arm
        Point { x: 0..=5, y: y@ (10 | 20 | 30) } => println!("On the y axis at {}", y),
        Point { x, y } => println!("On neither axis: ({}, {})", x, y),
    }
}
```

应用场景：

下面这段代码会报错

```plain
enum Message {
    Hello { id: i32 },
}

fn main() {
    let msg = Message::Hello { id: 5 };

    match msg {
        Message::Hello {
            id:  3..=7,
        } => println!("id 值的范围在 [3, 7] 之间: {}", id),//Error cannot find value `id` in this scope
        Message::Hello { id: newid@10 | 11 | 12 } => {//Error variable `newid` is not bound in all patterns pattern doesn't bind `newid`
            println!("id 值的范围在 [10, 12] 之间: {}", newid)
        }
        Message::Hello { id } => println!("Found some other id: {}", id),
    }
}
```

修复错误

```plain
enum Message {
    Hello { id: i32 },
}

fn main() {
    let msg = Message::Hello { id: 5 };

    match msg {
        Message::Hello {
            id: id @3..=7,
        } => println!("id 值的范围在 [3, 7] 之间: {}", id),
        Message::Hello { id: newid@(10 | 11 | 12) } => {
            println!("id 值的范围在 [10, 12] 之间: {}", newid)
        }
        Message::Hello { id } => println!("Found some other id: {}", id),
    }
}
```

# 二十三.unsafe Rust

- 隐藏着第二个语言，它没有强制内存安全保证：unsafe Rust(不安全的Rust)

和普通的Rust一样，但提供了额外的超能力

- Unsafe Rust存在的原因:

1. 静态分析是保守的，使用unsafe rust就相当于告诉编译器：我知道自己在做什么，并承担相应的风险
2. 计算机硬件本身就是不安全的，Rust需要能够进行底层系统编程

## unsafe超能力

- 使用unsafe关键字来切换到unsafe Rust，开启一个块，里面放着unsafe代码
- unsafe Rust里执行的四个动作（unsafe超能力):

1. 解引用原始指针
2. 调用unsafe函数或方法
3. 访问或修改可变的静态变量
4. 实现unsafe trait

- 注意：

unsafe并没有关系借用检查或停用其它安全的安全检查

任何内存安全相关的而错误必须留在unsafe块里

尽可能隔离unsafe代码，最好将其封装在安全的抽象里，提供安全的API

## 解引用原始指针

- 原始指针

可变的： *mut T

不可变的： *const T，意味着指针在解引用后不能直接对其进行赋值

注意：这里的*不是解引用符号，它是类型名的一部分

- 与引用不同，原始指针:

1. **允许通过同时具有可变和不可变指针或指向同一位置的可变指针来忽略借用规则**
2. 无法保证能指向合理的内存
3. 允许为null
4. 不实现任何自动清理

- 放弃保证的安全，换取更好的性能/与其它语言或硬件接口的能力

```plain
fn main(){
    let mut num=5;
    let r1 = &num as *const i32;
    let r2 = &mut num as *mut i32;
    let address = 0x012345usize;
    let r = address as *const i32;
}
```

可以在安全代码块里创建原始指针，但不能够解引用

```plain
fn main(){
    let mut num=5;
    let r1 = &num as *const i32;
    let r2 = &mut num as *mut i32;

    unsafe{
        println!("r1:{}",*r1);
        println!("r2:{}",*r2);
    }

    let address = 0x012345usize;
    let r = address as *const i32;
    unsafe{
        println!("r:{}",*r);
    }
}
```

为什么要使用原始指针？

- 与C语言进行接口
- 构建借用检查器无法理解的安全抽象

## 调用unsafe函数或方法

- unsafe函数或方法：在定义前加上了unsafe关键字

——调用前需手动满足一些条件（主要靠看文档），因为Rust无法对这些条件进行验证

——需要在unsafe块里进行调用

```plain
unsafe fn dangerous(){}
fn main(){
    unsafe{
        dangerous();
    }
}
```

## 创建unsafe代码的安全抽象

- 函数包含unsafe代码并不意味着需要将整个函数标记为unsafe
- 将unsafe代码包裹在安全函数中是一个常见的抽象

```plain
use std::vec;

fn split_at_mut(slice:&mut[i32],mid:usize)->(&mut [i32],&mut[i32]){
    let len = slice.len();
    assert!(mid<=len);
    (&mut slice[..mid],&mut slice[mid..])
}
fn main(){
    let mut v= vec![1,2,3,4,5,6];
    let r = &mut v[..];
    let (a,b) = r.split_at_mut(3);
    assert_eq!(a,&mut [1,2,3]);
    assert_eq!(b,&mut [4,5,6]);
}
```

报错

```plain
error[E0499]: cannot borrow `*slice` as mutable more than once at a time  
 --> src\main.rs:6:29
  |
3 | fn split_at_mut(slice:&mut[i32],mid:usize)->(&mut [i32],&mut[i32]){   
  |                       - let's call the lifetime of this reference `'1`
...
6 |     (&mut slice[..mid],&mut slice[mid..])
  |     ------------------------^^^^^--------
  |     |     |                 |
  |     |     |                 second mutable borrow occurs here
  |     |     first mutable borrow occurs here
  |     returning this value requires that `*slice` is borrowed for `'1`  

For more information about this error, try `rustc --explain E0499`.
```

使用unfase代码

```plain
use std::slice;
fn split_at_mut(slice: &mut [i32],mid: usize)->(&mut [i32],&mut [i32]){
    let len = slice.len();
    let ptr = slice.as_mut_ptr();
    assert!(mid<=len);
    unsafe{
        (
            slice::from_raw_parts_mut(ptr, mid),
            slice::from_raw_parts_mut(ptr.add(mid), len-mid)
        )
    }
}
fn main(){
    let mut v= vec![1,2,3,4,5,6];
    let r = &mut v[..];
    let (a,b) = r.split_at_mut(3);
    assert_eq!(a,&mut [1,2,3]);
    assert_eq!(b,&mut [4,5,6]);
}
```

## 使用extern函数调用外部代码

- extern 关键字：简化创建和使用外部函数接口（FFI）的过程
- 外部函数接口(FFI，Foreign Function Interface) : 它允许一种编程语言定义函数，并让其它编程语言能调用这些函数
- extern 块中声明的函数在 Rust 代码中总是不安全的。因为其他语言不会强制执行 Rust 的规则且 Rust 无法检查它们，所以确保其安全是程序员的责任

```plain
extern "C"{//"C"指明外部函数应用的二进制接口abi(application binary interface)
    fn abs(input: i32) ->i32;//想要调用的外部函数的签名
}
fn main(){
    unsafe{
        println!("Absolute value of -3 according to C:{}",abs(-3));
    }
}
```

- 应用二进制接口(ABI，Application Binary Interface):定义函数在汇编层的调用方式
- “C" ABI是最常见的ABI,它遵循C语言的ABI

## 从其它语言调用Rust函数

- 可以使用extern创建接口，其它语言通过它们可以调用Rust函数
- 在fn前添加extern关键字，并指定ABI
- 还需添加#[no_mangle]注解：避免Rust在编译时改变它的名称

```plain
#[no_mangle]
pub extern "C" fn call_from_c(){
    println!("Just called a Rust function from C!");//编译链接后就可被c语言访问了，extern 的使用无需 unsafe。
}

fn main(){
    
}
```

## 访问或修改一个可变的静态变量

- Rust支持全局变量，但因为所有权机制可能产生某些问题，例如数据竞争
- 在Rust里，全局变量叫做静态(static)变量

```plain
static HELLO_WORLD: &str = "Hello, world!";

fn main() {
    println!("name is: {}", HELLO_WORLD);
}
```

- 静态（static）变量类似常量。
- 通常静态变量的名称采用 SCREAMING_SNAKE_CASE 写法。
- 静态变量只能储存拥有 'static 生命周期的引用，这意味着 Rust 编译器可以自己计算出其生命周期而无需显式标注。
- 访问**不可变静态变量**是安全的。

静态变量和常量的区别：

- 静态变量中的值**有一个固定的内存地址**。使用这个值总是会访问相同的地址。常量则允许在任何被用到的时候**复制其数据**。
- 静态变量可以是可变的。访问和修改可变静态变量都是 **不安全** 的。

```plain
static mut COUNTER: u32 = 0;

fn add_to_count(inc: u32) {
    unsafe {
        COUNTER += inc;
    }
}

fn main() {
    add_to_count(3);

    unsafe {
        println!("COUNTER: {}", COUNTER);
    }
}
```

任何读写 COUNTER 的代码都必须位于 unsafe 块中。这段代码可以编译并如期打印出 COUNTER: 3，因为这是单线程的。拥有多个线程访问 COUNTER 则可能导致数据竞争。

拥有可以全局访问的可变数据，难以保证不存在数据竞争，这就是为何 Rust 认为可变静态变量是不安全的。任何可能的情况，优先使用智能指针，这样编译器就能检测不同线程间的数据访问是否是安全的。

## 实现不安全trait

- 当 trait 中至少有一个方法中包含编译器无法验证的不变式（invariant）时 trait 是不安全的。
- 可以在 trait 之前增加 unsafe 关键字将 trait 声明为 unsafe，同时 trait 的实现也必须标记为 unsafe

```plain
unsafe trait Foo {
    // methods go here
}

unsafe impl Foo for i32 {
    // method implementations go here
}

fn main() {}
```

Sync 和 Send 标记 trait，编译器会自动为完全由 Send 和 Sync 类型组成的类型自动实现他们。如果实现了一个包含一些不是 Send 或 Sync 的类型，比如裸指针，并希望将此类型标记为 Send 或 Sync，则必须使用 unsafe。Rust 不能验证我们的类型保证可以安全的跨线程发送或在多线程间访问，所以需要我们自己进行检查并通过 unsafe 表明。

## 访问联合体中的字段

仅适用于 unsafe 的最后一个操作是访问 **联合体** 中的字段，union 和 struct 类似，但是在一个实例中同时只能使用一个声明的字段。联合体主要用于和 C 代码中的联合体交互。访问联合体的字段是不安全的，因为 Rust 无法保证当前存储在联合体实例中数据的类型。可以查看 (https://doc.rust-lang.org/reference/items/unions.html) 了解有关联合体的更多信息。

## 何时使用不安全的代码

使用 unsafe 来进行这五个操作（超能力）之一是没有问题的，甚至是不需要深思熟虑的，不过使得 unsafe 代码正确也实属不易，因为编译器不能帮助保证内存安全。当有理由使用 unsafe 代码时，是可以这么做的，通过使用显式的 unsafe 标注可以更容易地在错误发生时追踪问题的源头。

# 二十四.高级trait

## 在trait定义中使用关联类型来指定占位类型

- 关联类型(associate type)是trait中的类型占位符，它可以用于trait的方法签名中：

——可以定义出包含某些类型的trait，而在实现前无需知道这些类型是什么

```plain
pub trait Iterator {
    type Item;
    fn next(&mut self)->Option<Self::Item>;
}
fn main(){
    println!("Hello World");
}
```

## 关联类型与泛型的区别

| **泛型**                                        | **关联类型**                    |
| ----------------------------------------------- | ------------------------------- |
| 每次实现Trait时标注类型                         | 无需标注类型                    |
| 可以为一个类型多次实现某个Trait(不同的泛型参数) | 无法为单个类型多次实现某个Trait |

```plain
pub trait Iterator {
    type Item;
    fn next(&mut self) -> Option<Self::Item>;
}
pub trait Iterator2<T>{
    fn next(&mut self) -> Option<T>;
}
struct Counter{

}
impl Iterator for Counter{//只能实现一次
    type Item=u32;
    fn next(&mut self) -> Option<Self::Item> {
        None
    }
}
// impl Iterator for Counter{//只能实现一次,第二次为String实现报错
//     type Item=String;
//     fn next(&mut self) -> Option<Self::Item> {
//         None
//     }
// }

impl Iterator2<String> for Counter {
    fn next(&mut self) -> Option<String> {
        None
    }
}

impl Iterator2<u32> for Counter {//可以为不同的类型实现多次
    fn next(&mut self) -> Option<u32> {
        None
    }
}

fn main(){

}
```

关联类型主要用于提升代码的可读性，例如以下代码 :

```plain
pub trait CacheableItem: Clone + Default + fmt::Debug + Decodable + Encodable {
  type Address: AsRef<[u8]> + Clone + fmt::Debug + Eq + Hash;
  fn is_null(&self) -> bool;
}
```

相比 AsRef<[u8]> + Clone + fmt::Debug + Eq + Hash， Address 的使用可以极大的减少其它类型在实现该特征时所需的模版代码.

例子：使用关联类型：

```plain
struct Container(i32, i32);

// 使用关联类型实现重新实现以下特征
// trait Contains {
//    type A;
//    type B;

trait Contains<A, B> {
    fn contains(&self, _: &A, _: &B) -> bool;
    fn first(&self) -> i32;
    fn last(&self) -> i32;
}

impl Contains<i32, i32> for Container {
    fn contains(&self, number_1: &i32, number_2: &i32) -> bool {
        (&self.0 == number_1) && (&self.1 == number_2)
    }
    // Grab the first number.
    fn first(&self) -> i32 { self.0 }

    // Grab the last number.
    fn last(&self) -> i32 { self.1 }
}

fn difference<A, B, C: Contains<A, B>>(container: &C) -> i32 {
    container.last() - container.first()
}

fn main() {
    let number_1 = 3;
    let number_2 = 10;

    let container = Container(number_1, number_2);

    println!("Does container contain {} and {}: {}",
        &number_1, &number_2,
        container.contains(&number_1, &number_2));
    println!("First number: {}", container.first());
    println!("Last number: {}", container.last());
    
    println!("The difference is: {}", difference(&container));
}
```

实现

```plain
struct Container(i32, i32);

// A trait which checks if 2 items are stored inside of container.
// Also retrieves first or last value.
trait Contains {
    // Define generic types here which methods will be able to utilize.
    type A;
    type B;

    fn contains(&self, _: &Self::A, _: &Self::B) -> bool;
    fn first(&self) -> i32;
    fn last(&self) -> i32;
}

impl Contains for Container {
    // Specify what types `A` and `B` are. If the `input` type
    // is `Container(i32, i32)`, the `output` types are determined
    // as `i32` and `i32`.
    type A = i32;
    type B = i32;

    // `&Self::A` and `&Self::B` are also valid here.
    fn contains(&self, number_1: &i32, number_2: &i32) -> bool {
        (&self.0 == number_1) && (&self.1 == number_2)
    }
    // Grab the first number.
    fn first(&self) -> i32 { self.0 }

    // Grab the last number.
    fn last(&self) -> i32 { self.1 }
}

fn difference<C: Contains>(container: &C) -> i32 {
    container.last() - container.first()
}

fn main() {
    let number_1 = 3;
    let number_2 = 10;

    let container = Container(number_1, number_2);

    println!("Does container contain {} and {}: {}",
        &number_1, &number_2,
        container.contains(&number_1, &number_2));
    println!("First number: {}", container.first());
    println!("Last number: {}", container.last());
    
    println!("The difference is: {}", difference(&container));
}
```

## 默认泛型参数和运算符重载

- 可以在使用泛型参数时为泛型指定一个默认的具体类型
- 语法：<PlaceholderType=ConcreteType>
- 这种结束常用于运算符重载（operator overloading）
- Rust不允许创建自己的运算符及重载任意的运算符
- 但可以通过实现std::ops中列出的那些trait来重载一部分相应的运算符

```plain
use std::ops::Add;

#[derive(Debug, PartialEq)]
struct Point {
    x: i32,
    y: i32,
}
impl Add for Point {
    type Output = Point;
    fn add(self, other: Point) -> Point {
        Point {
            x: self.x + other.x,
            y: self.y + other.y,
        }
    }
}
fn main() {
    assert_eq!(
        Point { x: 1, y: 0 } + Point { x: 2, y: 3 },
        Point { x: 3, y: 3 }
    );
}
```

这里使用的是add的默认泛型参数self

```plain
use std::ops::Add;
struct Millimeters(u32);
struct Meter(u32);
impl Add<Meter> for Millimeters {
    type Output = Millimeters;
    fn add(self, rhs: Meter) -> Self::Output {
        Millimeters(self.0+(other.0*1000))
    }
}
fn main(){
    
}
```

这里指定泛型参数

## 默认泛型参数的主要应用场景

- 扩展一个类型而不破坏现有的代码
- 允许在大部分用户都不需要的特定场景下进行自定义

## 完全限定语法(Fully Qualified Syntax)

```plain
trait Pilot {
    fn fly(&self);
}
trait Wizard {
    fn fly(&self);
}
struct Human;

impl Pilot for Human {
    fn fly(&self) {
        println!("This is your captain speaking");
    }
}
impl Wizard for Human {
    fn fly(&self) {
        println!("Up!");
    }
}
impl Human {
    fn fly(&self){
        println!("*waving arms furiously*");
    }
}
fn main(){
    let person = Human;
    person.fly();//调用本身的方法
    Pilot::fly(&person);//调用Pilot trait中的方法
    Wizard::fly(&person);//调用Wizard trait中的方法
}
```

无参的形式

```plain
trait Animal {
    fn baby_name()->String;
}
struct Dog;

impl Dog {
    fn baby_name()->String{
        String::from("Spot")
    }
}
impl Animal for Dog {
    fn baby_name()->String {
        String::from("puppy")
    }
}

fn main(){
    println!("A baby dog is called a{}",Dog::baby_name());
    println!("A baby dog is called a{}",Animal::baby_name());//报错
}
```

这里的baby_name没有参数，编译器不知道是哪个Dog调用

- 完全限定语法：<Type as Trait>::function(receiver_if_method,netx_arg,..) ;
- 可以在任何调用函数或方法的地方使用
- 允许忽略那些从其他上下文能推导出来的部分
- 当Rust无法区分你期望调用哪个具体实现的时候，才需要使用这种语法

```plain
trait Animal {
    fn baby_name()->String;
}
struct Dog;

impl Dog {
    fn baby_name()->String{
        String::from("Spot")
    }
}
impl Animal for Dog {
    fn baby_name()->String {
        String::from("puppy")
    }
}

fn main(){
    println!("A baby dog is called a{}",Dog::baby_name());
    println!("A baby dog is called a{}",<Dog as Animal>::baby_name());
}
```

## 使用supertrait来要求trait附带其它trait的功能

- 需要在一个trait中使用其它trait的功能

——需要被依赖的trait也被实现

——那个被间接以来的trait就是当前trait的supertrait

```plain
use std::fmt::{self, write};
trait OutlinePrint: fmt::Display{
    fn outline_print(&self){
        let output = self.to_string();
        let len = output.len();
        println!("{}","*".repeat(len+4));
        println!("*{}*"," ".repeat(len+2));
        println!("* {} *",output);
        println!("*{}*"," ".repeat(len+2));
        println!("{}","*".repeat(len+4));
    }
}
struct Point{
    x:i32,
    y:i32,
}
impl OutlinePrint for Point {
}
impl fmt::Display for Point {
    fn fmt(&self, f: &mut fmt::Formatter<'_>) -> fmt::Result {
        write!(f, "({},{})",self.x,self.y)
    }
}

fn main(){
    
}
```

## 使用newtype模式在外部类型上实现外部trait

- 孤儿类型：只有当trait或类型定义在本地包时，才能为该类型实现这个trait
- 可以通过newtype模式来绕过这一规则

——利用tuple struct（元组结构体）创建一个新的类型

（例子）

```plain
use std::fmt;
struct Wrapper(Vec<String>);

impl fmt::Display for Wrapper{
    fn fmt(&self, f: &mut fmt::Formatter<'_>) -> fmt::Result {
        write!(f,"[{}]",self.0.join(", "))
    }
}
fn main(){
    let w = Wrapper(vec![String::from("hello"),String::from("world")]);
    println!("w={}",w);
}
```

# 二十五.高级类型

## 使用newtype模式实现类型安全和抽象

- newtype模式可以：

——用来静态的保证各种值之间不会混淆并表明值的单位

——为类型的某些细节提供抽象能力

——通过轻量级的封装来隐藏内部实现细节

## 适用类型别名创建类型同义词

- Rust提供了类型别名的功能：——为现有的类型生产另外的名称（同义词）——并不是一个独立的类型——使用type关键字
- 主要用途：减少代码的字符重复

```plain
fn takes_long_type(f: Box<dyn Fn()+Send+'static>){
    //snip
}
fn returns_long_type()->Box<dyn Fn()+Send+'static>{
    Box::new(|| println!("hi"))
}
fn main(){
    let f:Box<dyn Fn()+Send+'static> = Box::new(|| println!("hi"));
}
```

使用类型别名

```plain
type Thunk = Box<dyn Fn()+Send+'static>;
fn takes_long_type(f: Thunk){
    //snip
}
fn returns_long_type()->Thunk{
    Box::new(|| println!("hi"))
}
fn main(){
    let f:Thunk = Box::new(|| println!("hi"));
}
use std::io::Error;
use std::fmt;
pub trait Write {
    fn write(&mut self,buf: &[u8])->Result<usize,Error>;
    fn flush(&mut self)->Result<(),Error>;
    fn write_all(&mut self,buf: &[u8])->Result<(),Error>;
    fn write_fmt(&mut self,fmt: fmt::Arguments)->Result<(),Error>;
}
fn main(){

}
```

使用类型别名

```plain
use std::io::Error;
use std::fmt;
// type Result<T> = Result<T,std::io::Error>;标准库中定义了这个
type Result<T> = std::io::Result<T>;
pub trait Write {
    fn write(&mut self,buf: &[u8])->Result<usize>;
    fn flush(&mut self)->Result<()>;
    fn write_all(&mut self,buf: &[u8])->Result<()>;
    fn write_fmt(&mut self,fmt: fmt::Arguments)->Result<()>;
}
fn main(){

}
```

## never类型

- 有一个名为！的特殊类型：

——它没有任何值，行话成为空类型（empty type）

——我们倾向于叫它never类型，因为它在不反悔的函数中充当返回类型

- 不返回值的函数也被称作发散函数（diverging fuction)

```plain
fn bar() -> !{
    //return (),返回了单元类型,但是不可能创建出返回!类型的函数
}
fn main(){
    
}
fn main(){
    let guess = "";
    loop{
        let guess:u32 = match guess.trim().parse() {
            Ok(num) => num,
            Err(_) => continue,
        };
    }
}
```

match表达式要求各个分支返回的类型是相同的，而continue会返回never类型，该类型可以安全地强制转换为num所对应的类型

```plain
impl<T> Option<T>{
    pub fn unwrap(self) -> T{
        match self{
            Some(val) => val,
            None=>panic!("called `Option::unwrap()` on a `None` value"),
        }
    }
}
```

panic！返回never类型

## 动态大小和Sized Trait

- Rust 需要在编译时确定为一个特定类型的值分配多少空间
- 动态大小的类型（Dynamically Sized Types,DST）的概念:

—— 编写代码时使用只有在运行时才能确定大小的值

- str是动态大小的类型（注意不是&str）:只有运行时才能确定字符串的长度

—— 以下代码无法正常工作:

```plain
let s1:str = "Hello therel";
let s2:str = "How is it going";
```

因为他们都是同一个类型，需要的空间应该一样，但是这里声明时没有确定共同的空间

解决办法: 使用&str字符串切片类型

## Rust使用动态大小类型的通用方式

- 附带一些额外的元数据来存储动态信息的大小—— 使用动态大小类型时总会把它的值放在某种指针后面

## 另外一种动态大小的类型：trait

- 每个trait都是一个动态大小的类型，可以通过名称对其进行引用
- 为了将trait用作trait对象，必须将它放置在某种指针之后

—— 例如 &dyn Trait 或 Box<dyn Trait> (Rc<dyn Trait>) 之后

## Sized trait

- 为了处理动态大小的类型，Rust提供了一个Sized trait来确定一个类型的大小在编译时是否已知——**编译时可计算出大小的类型会自动实现这一trait**——**Rust还会为每一个泛型函数隐式的添加Sized约束**

```plain
fn generic<T>(t:T){
    
}
fn generic<T: Sized>(t:T){
}
fn main(){
    
}
```

- 默认情况下，泛型函数只能被用于编译时已经知道大小的类型，可以通过特殊语法来解除这一限制

## ?Sized trait约束

```plain
fn generic<T: ?Sized>(t:&T){
    
}
```

- T可能是也可能不是Sized
- 这个语法只能用在Sized上面，不能被用于其它trait

# 二十六.高级函数和闭包

## 函数指针

- 可以将函数传递给其它函数
- 函数在传递过程中会被强制转换为fn类型
- fn类型就是 **函数指针(function pointer)**

```plain
fn add_one(x:i32)->i32{
    x+1
}
fn do_twice(f: fn(i32)->i32,arg:i32)->i32{
    f(arg) + f(arg)
}
fn main(){
    let answer = do_twice(add_one, 5);
    println!("The answer is:{}",answer);
}
```

## 函数指针与闭包的不同

- fn是一个类型而不是一个trait

——可以直接指定fn为参数类型，不用声明一个以Fn trait为约束的泛型参数

- 函数指针实现了全部3种闭包trait（Fn,FnMut,FnOnce）:

——总是可以把函数指针用作参数传递给一个接受闭包的参数

——所以，倾向于搭配闭包trait的泛型来编写函数：可以同时接收闭包和普通函数

- 某些情景，只想接收fn而不接收闭包

——与外部不支持闭包的代码交互：C函数

```plain
fn main(){
    let list_of_numbers = vec![1,2,3];
    let list_of_strings:Vec<String> = list_of_numbers
    .iter()
    .map(|i| i.to_string())
    .collect();

    let list_of_numbers = vec![1,2,3];
    let list_of_strings: Vec<String> = list_of_numbers
    .iter()
    .map(ToString::to_string)
    .collect();
}
fn main(){
    enum Status{
        Value(u32),
        Stop,
    }
    let v = Status::Value(3);
    let list_of_statuses:Vec<Status> = (0u32..20).map(Status::Value).collect();
}
```

## 返回闭包

- 闭包使用trait进行表达，无法在函数中直接返回一个闭包，可以将一个实现了该trait的具体类型作为返回值

```plain
// fn returns_closure()->Fn(i32)->i32{//返回类型大小不固定
//     |x| x+1
// }

fn returns_closure() -> Box<dyn Fn(i32)->i32>{
    Box::new(|x| x+1)
} 

fn main(){

}
```

# 二十七.宏

[https://kaisery.github.io/trpl-zh-cn/ch19-06-macros.html#%E5%AE%8F](https://kaisery.github.io/trpl-zh-cn/ch19-06-macros.html#宏)

# 二十八.格式化输出

## 位置参数

```plain
fn main() {
    println!("{0}, this is {1}. {1}, this is {0}", "Alice", "Bob");// => Alice, this is Bob. Bob, this is Alice
    assert_eq!(format!("{1}{0}", 1, 2), "21");
    assert_eq!(format!("{1}{}{0}{}", 1, 2), "2112");
    println!("Success!")
}
```

## 具名参数

```plain
fn main() {
    println!("{argument}", argument = "test"); // => "test"

    assert_eq!(format!("{name}{}", 1, name = 2), "21");
    assert_eq!(format!("{a} {c} {b}",a = "a", b = 'b', c = 3 ), "a 3 b");
    
    // named argument must be placed after other arguments
    println!("{abc} {0}", 2, abc = "def");

    println!("Success!")
}
```

## 字符串对齐

默认情况下，通过空格来填充字符串

```plain
fn main() {
    // the following two are padding with 5 spaces
    println!("Hello {:5}!", "x"); // =>  "Hello x    !"  
    println!("Hello {:1$}!", "x", 5); // =>  "Hello x    !"

    assert_eq!(format!("Hello {1:0$}!", 5, "x"), "Hello x    !");
    assert_eq!(format!("Hello {:width$}!", "x", width = 5), "Hello x    !");

    println!("Success!")
}
```

左对齐, 右对齐, 使用指定的字符填充

```plain
fn main() {
    // left align
    println!("Hello {:<5}!", "x"); // => Hello x    !
    // right align
    assert_eq!(format!("Hello {:>5}!", "x"), "Hello     x!");
    // center align
    assert_eq!(format!("Hello {:^5}!", "x"), "Hello   x  !");

    // left align, pad with '&'
    assert_eq!(format!("Hello {:&<5}!", "x"), "Hello x&&&&!");

    println!("Success!")
}
```

我们还能使用 0 来填充数字

```plain
fn main() {
    println!("Hello {:5}!", 5); // => Hello     5!
    println!("Hello {:+}!", 5); // =>  Hello +5!
    println!("Hello {:05}!", 5); // => Hello 00005!
    println!("Hello {:05}!", -5); // => Hello -0005!

    assert!(format!("{number:0>width$}", number=1, width=6) == "000001");
    
    println!("Success!")
}
```

## 精度

浮点数精度

```plain
fn main() {
    let v = 3.1415926;

    println!("{:.1$}", v, 4); // same as {:.4} => 3.1416 

    assert_eq!(format!("{:.2}", v), "3.14");
    assert_eq!(format!("{:+.2}", v), "+3.14");
    assert_eq!(format!("{:.0}", v), "3");

    println!("Success!")
}
```

字符串长度

```plain
fn main() {
    let s = "Hello, world!";

    println!("{0:.5}", s); // => Hello

    assert_eq!(format!("Hello {1:.0$}!", 3, "abcdefg"), "Hello abc!");

    println!("Success!")
}
```

## 二进制，八进制，十六进制

```plain
fn main() {
    assert_eq!(format!("{:#b}", 27), "0b11011");
    assert_eq!(format!("{:#o}", 27), "0o33");
    assert_eq!(format!("{:#x}", 27), "0x1b");
    assert_eq!(format!("{:#X}", 27), "0x1B");

    println!("{:x}!", 27); // hex with no prefix => 1b

    println!("{:#010b}", 27); // pad binary with 0, width = 10,  => 0b00011011

   println!("Success!")
}
```

## 捕获环境中的值

```plain
fn get_person() -> String {
    String::from("sunface")
}

fn get_format() -> (usize, usize) {
    (4, 1)
}


fn main() {
    let person = get_person();
    println!("Hello, {person}!");

    let (width, precision) = get_format();
    let scores = [("sunface", 99.12), ("jack", 60.34)];
    /* Make it print:
    sunface:   99.1
    jack:   60.3
    */
    for (name, score) in scores {
        println!("{name}: {score:width$.precision$}");
    }
}
```

## 指数，指针地址，转义

```plain
fn main() {
    // 指数
    println!("{:2e}", 1000000000); // => 1e9
    println!("{:2E}", 1000000000); // => 1E9

    // 指针地址
    let v= vec![1, 2, 3];
    println!("{:p}", v.as_ptr()); // => 0x600002324050

    // 转义
    println!("Hello {{}}"); // => Hello {}
}
```

# Other

## 1.错误处理：

### unreachable!()

这是标记程序不应输入的路径的标准宏。如果程序进入这些路径，程序将panicked并返回"'internal error: entered unreachable code'"错误消息。



```rust
fn main() {
    let level = 22;
    let stage = match level {
        1...5 => "beginner",
        6...10 => "intermediate",
        11...20 => "expert",
        _ => unreachable!(),
    };

    println!("{}", stage);
}


// -------------- Compile time error --------------
thread 'main' panicked at 'internal error: entered unreachable code', src/main.rs:7:20
```

我们也可以为此设置自定义错误消息。



```rust
// --- with a custom message ---
_ => unreachable!("Custom message"),
// -------------- Compile time error --------------
thread 'main' panicked at 'internal error: entered unreachable code: Custom message', src/main.rs:7:20


// --- with debug data ---
_ => unreachable!("level is {}", level),
// -------------- Compile time error --------------
thread 'main' panicked at 'internal error: entered unreachable code: level is 22', src/main.rs:7:14
```

## 2.misconception corollaries

### 2.1 if `T: 'static` then `T` must be valid for the entire program

**Misconception Corollaries**

- `T: 'static` should be read as *"`T` has a `'static` lifetime"*
- `&'static T` and `T: 'static` are the same thing
- if `T: 'static` then `T` must be immutable
- if `T: 'static` then `T` can only be created at compile time

Most Rust beginners get introduced to the `'static` lifetime for the first time in a code example that looks something like this:

```rust
fn main() {
    let str_literal: &'static str = "str literal";
}
```



They get told that `"str literal"` is hardcoded into the compiled binary and is loaded into read-only memory at run-time so it's immutable and valid for the entire program and that's what makes it `'static`. These concepts are further reinforced by the rules surrounding defining `static` variables using the `static` keyword.

```rust
// Note: This example is purely for illustrative purposes.
// Never use `static mut`. It's a footgun. There are
// safe patterns for global mutable singletons in Rust but
// those are outside the scope of this article.

static BYTES: [u8; 3] = [1, 2, 3];
static mut MUT_BYTES: [u8; 3] = [1, 2, 3];

fn main() {
   MUT_BYTES[0] = 99; // ❌ - mutating static is unsafe

    unsafe {
        MUT_BYTES[0] = 99;
        assert_eq!(99, MUT_BYTES[0]);
    }
}
```



Regarding `static` variables

- they can only be created at compile-time
- they should be immutable, mutating them is unsafe
- they're valid for the entire program

The `'static` lifetime was probably named after the default lifetime of `static` variables, right? So it makes sense that the `'static` lifetime has to follow all the same rules, right?

Well yes, but a type *with* a `'static` lifetime is different from a type *bounded by* a `'static` lifetime. The latter can be dynamically allocated at run-time, can be safely and freely mutated, can be dropped, and can live for arbitrary durations.

It's important at this point to distinguish `&'static T` from `T: 'static`.

`&'static T` is an immutable reference to some `T` that can be safely held indefinitely long, including up until the end of the program. This is only possible if `T` itself is immutable and does not move *after the reference was created*. `T` does not need to be created at compile-time. It's possible to generate random dynamically allocated data at run-time and return `'static` references to it at the cost of leaking memory, e.g.

```rust
use rand;

// generate random 'static str refs at run-time
fn rand_str_generator() -> &'static str {
    let rand_string = rand::random::<u64>().to_string();
    Box::leak(rand_string.into_boxed_str())
}
```



`T: 'static` is some `T` that can be safely held indefinitely long, including up until the end of the program. `T: 'static` includes all `&'static T` however it also includes all owned types, like `String`, `Vec`, etc. The owner of some data is guaranteed that data will never get invalidated as long as the owner holds onto it, therefore the owner can safely hold onto the data indefinitely long, including up until the end of the program. `T: 'static` should be read as *"`T` is bounded by a `'static` lifetime"* not *"`T` has a `'static` lifetime"*. A program to help illustrate these concepts:

```rust
use rand;

fn drop_static<T: 'static>(t: T) {
    std::mem::drop(t);
}

fn main() {
    let mut strings: Vec<String> = Vec::new();
    for _ in 0..10 {
        if rand::random() {
            // all the strings are randomly generated
            // and dynamically allocated at run-time
            let string = rand::random::<u64>().to_string();
            strings.push(string);
        }
    }

    // strings are owned types so they're bounded by 'static
    for mut string in strings {
        // all the strings are mutable
        string.push_str("a mutation");
        // all the strings are droppable
        drop_static(string); // ✅
    }

    // all the strings have been invalidated before the end of the program
    println!("I am the end of the program");
}
```



**Key Takeaways**

- `T: 'static` should be read as *"`T` is bounded by a `'static` lifetime"*

- if `T: 'static` then `T` can be a borrowed type with a `'static` lifetime *or* an owned type

- since

   

  ```
  T: 'static
  ```

   

  includes owned types that means

  

  ```
  T
  ```

  - can be dynamically allocated at run-time
  - does not have to be valid for the entire program
  - can be safely and freely mutated
  - can be dynamically dropped at run-time
  - can have lifetimes of different durations
