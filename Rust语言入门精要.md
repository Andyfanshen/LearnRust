# Rust语言入门精要

写在最前：本篇文章基于《The Rust Programming Language》（2018），也就是大名鼎鼎的《The Book》一书，将其中关键的Rust语言知识要点总结整理后重新编排，用于学习过Rust语言的同学快速回顾Rust基本语法及其编程思路。

## 1. 工程构建与包管理工具 Cargo

参考原文链接：[Rust的安装、更新与卸载](https://kaisery.github.io/trpl-zh-cn/ch01-01-installation.html)

Rust的安装和工具链管理由`rustup`控制。安装完`rustup`后，可以通过指令`rustup update`更新`rustup`工具本身以及Rust的工具链，可以通过指令`rustup self uninstall`卸载`rustup`。

安装`rustup`的同时会自动安装Rust的工具链（如rustc、cargo、rustup），其中包括工程构建和包管理工具`cargo`。

安装结束后，通过指令`rustc --version`确认安装正确。如果出现问题，首先排查是否已经将Rust开发环境相关工具的目录导入进了系统变量中。

Rust的工具链，其默认安装位置都在`~/.cargo/bin`中。在我的Windows系统中，其具体路径为`C:\Users\UserName\.cargo`。

---

Rust源码可以使用编译工具`rustc`直接编译，但更通常的做法是借助项目管理工具`cargo`，故本文仅回顾基于`cargo`的开发流程，其他流程可参考[使用rustc直接编译Rust源码](https://kaisery.github.io/trpl-zh-cn/ch01-02-hello-world.html)。

检查`cargo`版本：`cargo --version`

使用`cargo`创建新的项目：`cargo new PROJECT_NAME`

使用`cargo`编译并运行当前项目（不优化）：`cargo run`

使用`cargo`构建当前项目（使用发布优化）：`cargo build --release`

使用`cargo`查看项目自动生成的文档：`cargo doc --open`

使用`cargo`进行编译检查：`cargo check`

使用`cargo`执行单元测试：`cargo test`

## 2. 基本语法

### 2.1 变量

参考原文链接：[变量与可变性](https://kaisery.github.io/trpl-zh-cn/ch03-01-variables-and-mutability.html)

---

Rust中声明变量

```rust
let x = 5;
```

Rust中的变量默认**不可变**，变量之间的赋值一般使用**移动语义**（但如果该类型是Copy的，则默认使用Copy语义），声明同名新变量会**隐藏**（Shadowing）原变量。

Rust中的**变量命名规范**：单词全小写，单词间使用下划线连接。

Rust编译器提供一定程度自动推断变量类型的功能。

---

Rust中声明**可变**变量：

```rust
let mut x = 6;
```

---

Rust中声明常量：

```rust
const THREE_HOURS_IN_SECONDS: u32 = 60 * 60 * 3;
```

Rust中声明常量**必须**注明类型，常量只能被设置为常量表达式。

Rust中的**常量命名规范**：单词全大写，单词间使用下划线连接。

---

Rust中的基本数据类型为：标量（scalar）和复合（compound）。

标量类型包括：整型、浮点型、布尔类型和字符类型。

整型：

| 长度      | 有符号     | 无符号       |
|:-------:|:-------:|:---------:|
| 8-bit   | i8      | u8        |
| 16-bit  | i16     | u16       |
| 32-bit  | **i32** | u32       |
| 64-bit  | i64     | u64       |
| 128-bit | i128    | u128      |
| arch    | isize   | **usize** |

整型字面值：

| 数字字面值               | 例子          |
|:-------------------:|:-----------:|
| Decimal (十进制)       | 98_222      |
| Hex (十六进制)          | 0xff        |
| Octal (八进制)         | 0o77        |
| Binary (二进制)        | 0b1111_0000 |
| Byte (单字节字符)(仅限于u8) | b'A'        |

浮点型：

Rust中有两个原生的浮点数类型，分别为`f32`和`f64`，默认类型为`f64`。

布尔型：

Rust中的布尔类型为`bool`，只有两种可能的值：`true`或`false`。

字符类型：

Rust中的字符类型为`char`，使用单引号声明字面量。

```rust
let c = 'z';
```

Rust中的`char`类型大小为四个字节，代表一个Unicode标量值。

需要和双引号声明的字符串字面量区分。

---

复合类型包括：元组（tuple）和数组（array）。

元组类型：

Rust中使用包含在圆括号中以逗号分隔的值列表来创建元组。

```rust
let tup: (i32, f64, u8) = (500, 6.4, 1);

let (x, y, z) = tup;

let one = tup.2;
```

元组长度固定，一旦声明便不可变。

不带任何值的元组被称为**单元**（unit）元组，写作`()`，表示空值或空的返回类型。

数组类型：

Rust中的数组具有固定长度。

```rust
let a = [1, 2, 3, 4, 5];

let a: [i32; 5] = [1, 2, 3, 4, 5];

let a = [3; 5];//等同于let a = [3, 3, 3, 3, 3];

let first = a[0];
```

### 2.2 函数

函数的声明与调用：

```rust
fn main(){
    println!("Hello, world");

    another_function();
}

fn another_function(){
    println!("Another function.");
}
```

Rust中的**函数命名规范**：单词全小写，单词间使用下划线连接。

Rust中声明函数签名时，**必须**声明每个参数的类型。

```rust
fn main(){
    print_labeled_measurement(5, 'h');
}

fn print_labeled_measurement(value: i32, unit_label: char) {
    println!("The measurement is: {value}{unit_label}");
}
```

Rust中通过表达式来提供返回值，因此，如果函数需要返回值，则需要给出**表达式**。

Rust中函数返回值类型的声明书写在箭头符号`->`后。

```rust
fn five() -> i32{
    5
}
```

Rust中，函数可以通过返回元组的方式返回多个值：

```rust
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

### 2.3 注释

行注释：

```rust
// 行注释
```

文档注释：

```rust
/// 文档注释
```

### 2.4 控制流

`if`表达式：

```rust
fn main(){
    let number = 3;

    if number < 5 {
        println!("condition was true");
    } else {
        println!("condition was false");
    }
}
```

Rust中，条件值**必须**是`bool`值。

Rust中，多重条件判断使用`else if`。（逻辑复杂的情况下推荐使用分支结构`match`）

---

在`let`语句中使用if表达式的值：

```rust
fn main(){
    let condition = true;
    let number = if condition { 5 } else { 6 }
}
```

Rust中，`if`表达式的结果为代码块作为表达式的结果，因此，各分支代码块的表达式类型必须相同。

---

Rust中有三种循环：`loop`、`while`和`for`。

`loop`循环：

```rust
fn main(){
    loop {
        println!("again!");
    }
}
```

Rust中，`loop`循环会无限循环，可以通过`break`跳出循环，使用`continue`则可以直接进入下一轮循环。

从循环中返回值：

```rust
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

使用循环标签消除歧义：

```rust
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

---

`while`条件循环：

```rust
fn main() {
    let mut number = 3;

    while number != 0 {
        println!("{number}!");

        number -= 1;
    }

    println!("LIFTOFF!!!");
}
```

---

`for`遍历循环：

```rust
fn main() {
    let a = [10, 20, 30, 40, 50];

    for element in a {
        println!("the value is: {element}");
    }
}
```

Rust中，`for`循环遍历迭代器，因此只有可迭代类型才可以使用`for`循环。

在`for`循环中使用`Range`语法：

```rust
fn main() {
    for number in 1..=5 {
        println!("{number}");
    }
}
```

## 3. 所有权

Rust中的所有权规则：

1. Rust中的每一个值都有一个**所有者**（owner）变量。

2. 值在任何时刻**有且只有**一个所有者。

3. 当所有者离开作用域，该值将被丢弃。

---

Rust中变量与数据交互的方式有三种：

1. 移动（move）

2. 克隆（clone）

3. 借用（borrow）

---

Rust中默认对变量间的赋值使用**移动**语义移动数据；但如果该类型具备**Copy trait**（即基本标量类型，或是由基本标量类型复合的元组），则赋值过程采用Copy（Clone）方法复制数据。

```rust
let x = 5;
let y = x; //copy

let s1 = String::from("hello");
let s2 = s1; //move
```

除赋值外，函数的参数传递以及返回值也具有相似的处理方式。

---

Rust中，通过借用的方式引用数据：

```rust
fn main() {
    let s1 = String::from("hello");

    let len = calculate_length(&s1);

    println!("The length of '{}' is {}.", s1, len);
}

fn calculate_length(s: &String) -> usize {
    s.len()
}
```

Rust中，通过`&`符号来生成值的`引用`（reference），这允许用户在不获取所有权的情况下借用该数据。与其他语言保持一致，`解引用`（dereferencing）使用运算符`*`。

Rust中，借用行为不获取所有权，因此（默认）不允许修改引用的值；如果需要修改引用的值，可以通过**可变引用**（mutable reference）：

```rust
fn main() {
    let mut s = String::from("hello");

    change(&mut s);
}

fn change(some_string: &mut String) {
    some_string.push_str(", world");
}
```

Rust为了避免数据竞争，对于某个特定数据，在同一时间只能存在**一个**可变引用。另外，为了避免**不可变**的约定被破坏，一个数据无法同时具备可变引用与不可变引用。

借用规则：

1. 同一作用域，有且仅有一个可变引用。

2. 同一作用域，可以有一个或多个不可变引用。

3. 引用的作用域为：从声明该引用到最后一次使用该引用。

下列代码符合借用规则：

```rust
fn main() {
    let mut s = String::from("hello");

    let r1 = &s; // 没问题
    let r2 = &s; // 没问题
    println!("{} and {}", r1, r2);
    // 此位置之后 r1 和 r2 不再使用

    let r3 = &mut s; // 没问题
    println!("{}", r3);
}
```

## 4. 结构体

结构体的定义：

```rust
struct User {
    active: bool,
    username: String,
    email: String,
    sign_in_count: u64,
}
```

结构体中的每个数据称为**字段**（field）。

使用结构体时，需要创建一个**实例**：

```rust
let user1 = User {
    email: String::from("someone@example.com"),
    username: String::from("someusername123"),
    active: true,
    sign_in_count: 1,
}; 
```

Rust中，实例必须是**可变的**。

Rust中，可使用**字段初始化简写语法**（field init shorthand）省略指定同名变量：

```rust
fn build_user(email: String, username: String) -> User {
    User {
        email,
        username,
        active: true,
        sign_in_count: 1,
    }
}
```

Rust中，可使用**结构体更新语法**通过其他实例的字段创建实例：

```rust
let user2 = User {
        email: String::from("another@example.com"),
        ..user1
};
```

但需要注意，在该例中，`user1`的`username`字段被`move`进`user2`中，从而使得`user1`无效。如果使用的字段类型是满足`Copy trait`的，则不受影响。

---

Rust中可以定义**元组结构体**（tuple structs）：

```rust
struct Color(i32, i32, i32);
struct Point(i32, i32, i32);

fn main() {
    let black = Color(0, 0, 0);
    let origin = Point(0, 0, 0);
}
```

相当于为特定元组取了个名字。元组结构体无需为字段取名。

---

Rust中，没有定义任何字段的结构体被称为**类单元结构体**（unit-like structs），常用于为特定类型实现trait：

```rust
struct AlwaysEqual;

fn main() {
    let subject = AlwaysEqual;
}
```

---

Rust中，在结构体（或枚举或trait对象）的上下文中定义**与实例相关的**函数被称为**方法**（method），方法的第一个参数总是`self`，代表调用该方法的实例：

```rust
impl Rectangle {
    fn area(&self) -> u32 {
        self.width * self.height
    }
}
```

其中`&self`实际上是`self: &Self`的缩写；在`impl`块中，`Self`类型是`impl`块类型的别名（此处即为Rectangle）。

当方法需要实例的可变借用时，则签名使用`&mut self`。

当方法需要实例的所有权时（比如转换实例类型的同时移交所有权），则签名使用`self`。

---

`impl`块中也可以定义**与实例无关**的函数，它们不再是方法，而被称为**关联函数**（associated functions），构造函数就是一类很常见的关联函数：

```rust
impl Rectangle {
    fn square(size: u32) -> Rectangle {
        Rectangle {
            width: size,
            height: size,
        }
    }
}
```

通过结构体名和`::`语法调用关联函数，例如`let sq = Rectangle::square(3);`。

---

如果在结构体中持有引用，则需要标注**生命周期**。

关于结构体的重构过程可以概括为：业务代码→使用元组重构→使用结构体重构→派生trait增加功能→实现方法和关联函数增加功能。

允许拥有多个`impl`块。

## 5. 枚举

为一种自定义类型枚举出所有可能的值，便适合枚举类型。

枚举类型的定义由枚举**成员**（variants）构成：

```rust
enum IpAddrKind {
    V4,
    V6,
}
```

通过`::`来指定具体的**枚举值**：

```rust
let four = IpAddrKind::V4;
```

可以定义函数来获取任何`IpAddrKind`：

```rust
fn route(ip_kind: IpAddrKind) {}
```

枚举成员也可以直接存储数据：

```rust
enum IpAddr {
    V4(u8, u8, u8, u8),
    V6(String),
}

let home = IpAddr::V4(127, 0, 0, 1);

let loopback = IpAddr::V6(String::from("::1"));
```

枚举成员中可以存入任何类型的数据，甚至是另一个枚举类型。

---

Rust中有一种广泛使用的标准库枚举类型：`Option`

```rust
enum Option<T> {
    None,
    Some(T),
}
```

`Option`用于处理**有值或空值**的情况。Rust中不设专门的空值（例如`Null`），但通过枚举类型`Option<T>`来处理值可能为空的情况：

```rust
let some_number = Some(5);

let some_string = Some("a string");

let absent_number: Option<i32> = None;
```

当取用`None`值时，编译器无法自动推断`Option<T>`的类型，所以需要指定类型。

---

从枚举类型中取出数据有很多种方法，最常见的是使用`match`表达式：

```rust
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
```

`match`表达式可以绑定匹配模式的部分值，也就是从枚举成员中提取值：

```rust
fn plus_one(x: Option<i32>) -> Option<i32> {
    match x {
        None => None,
        Some(i) => Some(i + 1),
    }
}
```

`match`表达式要求，匹配**必须**是**穷尽的**。但有时我们只对其中几个特定的值感兴趣，那么对于剩下的值就可以使用通配模式和占位符`_`：

```rust
let number = 9;
match number{
    3 => (),
    7 => (),
    other => println!("{other}"),
}
```

该例中的`other`将会绑定除`3`和`7`之外的一切值；但如果不需要绑定值，则可以使用占位符`_`：

```rust
let number = 9;
match number{
    3 => (),
    7 => (),
    _ => println!("other number"),
}
```

---

`match`表达式要求**穷尽**所有可能，但有时我们只关注于某个特定的情况，这时使用`if let`会更加方便：

```rust
let config_max = Some(3u8);
if let Some(max) = config_max {
    println!("The maximum is configured to be {}", max);
}
```

`if let`可以减少代码，但也意味着失去`match`的穷尽性检查。

`if let`之后可以跟随`else`分支。

## 6. 项目管理

Rust的项目被称为**包**（package），一个包含有一个`Cargo.toml`文件，该文件描述如何组织和构建其中的`crate`。

`crate`是编译的最基本单元，一个包中可以含有至多一个**库crate**（library crate）和任意数量的**二进制crate**（binary crate）。

`Cargo`约定，若存在`src/lib.rs`，则包带有与其同名的库crate；若存在`src/main.rs`，则包带有与其同名的二进制crate；其他的二进制crate可以放在`src/bin`目录中。

通过指令`cargo run -bin name`运行指定的二进制crate（也可以在`Cargo.toml`中指定`default-run`）。

`crate`是一种树形**模块**（module）结构，而`src/lib.rs`和`src/main.rs`分别是库crate和二进制crate的根节点。

---

**模块**（module），类似于其他语言中的命名空间，用于控制文件结构、作用域和可见性。

模块的定义：

```rust
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

引用模块需要使用项目路径，这包含两种形式：

- **绝对路径**（absolute path）从crate根节点开始，以crate的名称或者`crate`开头（补充一下，与package同名的crate指的是`lib.rs`且只能在二进制crate中使用，而`crate`关键字指的是当前的crate）。

- **相对路径**（relative path）从当前模块开始，以`self`、`super`或当前模块的标识符开头。

较长的路径可以使用关键字`use`引入作用域，并使用关键字`as`指定别名。

Rust中默认所有项（函数、方法、结构体、枚举、模块和常量）都是**私有的**。

父模块中的项**不能**使用子模块中的私有项，子模块中的项**可以**使用父模块中的项。

可以通过`pub`关键字来创建公共项，使子模块的内容暴露给上级模块。

```rust
mod front_of_house {
    pub mod hosting {
        pub fn add_to_waitlist() {}
    }
}

pub fn eat_at_restaurant() {
    // 绝对路径
    crate::front_of_house::hosting::add_to_waitlist();

    // 相对路径
    front_of_house::hosting::add_to_waitlist();
}
```

Rust中，枚举类型如果用关键字`pub`标注，则枚举成员也将全部公开；但结构体类型的字段仍然保持私有，除非也用关键字`pub`标注。

使用关键字`pub use`可以对导入的内容**重导出**（re-exporting），则他人也可以将这些内容导入他们的作用域中。

大型项目中常常会将模块的定义和声明分离，可以在`src/lib.rs`中声明模块：

```rust
mod front_of_house;

pub use crate::front_of_house::hosting;

pub fn eat_at_restaurant() {
    hosting::add_to_waitlist();
    hosting::add_to_waitlist();
    hosting::add_to_waitlist();
}
```

上述对`front_of_house`模块的声明会告诉编译器去寻找与模块同名的文件，并加载其中的模块定义。

并在`src/front_of_house.rs`中定义该模块：

```rust
pub mod hosting {
    pub fn add_to_waitlist() {}
}
```

我们甚至可以进一步拆散`front_of_house.rs`文件中的内容：

```rust
pub mod hosting;
```

然后再创建一个`src/front_of_house/hosting.rs`文件来描述`hosting`模块的定义：

```rust
pub fn add_to_waitlist() {}
```

简单来说，当单个文件内容较大的时候，可以在同级目录下创建一个与该文件同名的文件夹，然后将文件中的模块拆分成多个独立的文件装入这个文件夹中。模块之间的组织关系仍然维持不变，但将复杂的模块结构拆散到多个文件中可以控制单个文件的大小。

另一种组织模块的方式是：

1. 在某个文件夹下创建`mod.rs`文件

2. `mod.rs`中声明的模块都将自动声明在与文件夹同名的模块下

3. 在文件夹中组织各模块的定义文件

---

Rust项目中可以使用来自外部（比如[crates.io](https://crates.io)）的crate。将外部crate引入自己项目的步骤是：

1. 在`Cargo.toml`中列出

2. 通过`use`引入项目的作用域

其实，标准库（std）也是外部crate，但Rust语言中一般包含标准库，所以不需要在`Cargo.toml`中列出，可以直接通过`use`引入。

## 7. 集合

### 7.1 vector

Rust中创建vector：

```rust
let v: Vec<i32> = Vec::new();
```

但通常会使用`vec!`宏创建vector：

```rust
let v = vec![1, 2, 3];
```

如果需要向vector中加入新的元素，那就需要用`mut`修饰：

```rust
let mut v = Vec::new();

v.push(5);
```

读取vector中的元素可以通过**索引**或者`get`：

```rust
let v = vec![1, 2, 3];

println!("{}", &v[2]);

println!("{}", v.get(2).unwrap());
```

Rust中一般使用`for`循环遍历vector：

```rust
let v = vec![100, 32, 57];
for i in &v {
    println!("{}", i);
}
```

当需要改变元素时，需要用`mut`修饰：

```rust
let mut v = vec![100, 32, 57];
for i in &mut v {
    *i += 50;
}
```

Rust中，可以在vector内存储枚举类型：

```rust
enum SpreadsheetCell{
    Int(i32),
    Float(f64),
    Text(String),
}

let row = vec![SpreadsheetCell::Int(3),
    SpreadsheetCell::Text(String::from("blue")),
    SpreadsheetCell::Float(10.12),
];
```

### 7.2 String

`String`是由标准库提供的一种字符串类型，相较于Rust的基础字符串`str`类型更加实用。

`str`是Rust的基础字符串类型，一般以字面值的形式存在，无法获取所有权（不可修改），通常以借用的形式`&str`出现，常使用其切片：

```rust
let s = "Hello, world!";
println!("{}", &s[2..]);
```

而`String`类型是可增长的、可变的、有所有权的、UTF-8编码的字符串类型。

新建`String`字符串：

```rust
let mut s = String::new();
```

当有初始值时，可以通过`to_string`或者`String::from`来创建字符串：

```rust
let s = "initial contents".to_string();
let s = String::from("initial contents");
```

Rust中，任何实现了`Display trait`的类型都可以使用`to_string`方法。

更新字符串的方式很多，可以使用`+`运算符，也可以使用`format!`宏拼接，也可以使用`push_str`和`push`附加：

```rust
//使用+运算符
let s1 = String::from("Hello, ");
let s2 = String::from("world");
let s = s1 + &s2;//s1的所有权被移动到s中

//使用format!宏
let s1 = String::from("world");
let s2 = String::from("Hello");
let s = format!("{s2}, {s1}!");

//使用push_str附加String
let mut s = String::from("Hello, ");
s.push_str("world!");

//使用push附加char
let mut s = String::from("Hello");
s.push('!');
```

Rus中不能直接索引`String`字符串，因为UTF-8是变长编码，一个字形簇可能对应着多个字节，贸然索引可能会引发问题。

Rust中遍历字符串一般使用`chars`方法来返回Unicode标量值的迭代器：

```rust
for c in "你好".chars(){
    println!("{c}");
}
```

特殊需求下可以使用`bytes`方法返回原始字节的迭代器：

```rust
for b in "你好".bytes(){
    println!("{b}");
}
```

### 7.3 Hash Map

新建`HashMap`：

```rust
use std::collections::HashMap;
let mut scores = HashMap::new();

scores.insert(String::from("Blue"), 10);
scores.insert(String::from("Yellow"), 50);
```

Rust中的`HashMap`并没有被prelude自动引用，需要从标准库中引入。

另一种构建`HashMap`的方式是在元组的`vector`上使用迭代器和`collect`方法：

```rust
use std::collections::HashMap;

let teams = vec![String::from("Blue"), String::from("Yellow")];
let initial_scores = vec![10, 50];

let mut scores: HashMap<_, _> =
    teams.into_iter().zip(initial_scores.into_iter()).collect();
```

`zip`将两个迭代器组合成一个元组迭代器，再通过该元组迭代器上的`collect`方法收集成`HashMap`。

在`HashMap`中插入数据也采用与赋值相似的处理方式，即`Copy trait`类型被拷贝进`HashMap`中，而其他类型则是被移动进`HashMap`中。

---

`HashMap`获取值通过`get`方法：

```rust
let team_name = String::from("Blue");
let score = scores.get(&team_name);//Option<Value>
```

`HashMap`是可以遍历的：

```rust
for (key, value) in &scores {
    println!("{}: {}", key, value);
}
```

`HashMap`中覆盖一个值：

```rust
scores.insert(String::from("Blue"), 10);
scores.insert(String::from("Blue"), 25);//用25覆盖原值10
```

先检查是否为空再插入值：

```rust
scores.entry(String::from("Blue")).or_insert(50);
```

`or_insert`方法返回该值的可变引用，可以据此进行一些处理：

```rust
let count = scores.entry(String::from("Blue")).or_insert(0);
*count += 1;
```

## 8. 错误处理
