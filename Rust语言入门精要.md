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

Rust中，函数可以通过`return`提前返回值：

```rust
fn add_one(number: i32, flag: bool) -> i32 {
    if flag {
        return number + 1;
    }
    number
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

文档注释可以生成HTML文档，这些文档用于展示公有API的内容，通常用于描述如何**使用**这些接口。

文档注释支持Markdown注解来格式化文本：

```rust
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

该文档注释给出了`add_one`函数工作的描述，然后开始了一个标题为`Examples`的部分演示`add_one`函数的使用。

可以使用`cargo doc`指令来生成文档注释的HTML文档，该指令由`rustdoc`处理并在`target/doc`目录中生成文档。方便起见，可以通过`cargo doc --open`完成文档的生成和打开。

除了`# Examples`这样的部分外，文档注释中经常使用的部分还有：

- **Panics**：该函数可能`panic!`的场景，指导调用者避免这种情况。

- **Errors**：描述该函数可能会出现哪些错误以及错误的原因，指导调用者采用合适的方式处理这些错误。

- **Safety**：如果该函数使用了`unsafe`的代码，该部分指导调用者如何确保`unsafe`的代码正常工作。

另外，文档注释可以用作代码测试，详情见自动化测试部分。

还有另一种风格的文档注释：

```rust
//!
```

该文档注释是对当前所在的层级增加注释的。例如在名为`my_crate`的crate项目中，如果希望对`my_crate`加以注释说明，就可以这样写：

```rust
//! # My Crate
//!
//! `my_crate` is a collection of utilities to make performing certain
//! calculations more convenient.
```

通常是`src/lib.rs`文件中包含这类文档注释。

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

结构体的**命名规范**：单词首字母大写，单词直接相连（Pascal命名规范）。

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

枚举类型的**命名规范**与结构体一致（Pascal命名规范）。

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

使用关键字`pub use`还可以重导出合适的公有API以方便调用者使用：

```rust
pub use self::kinds::PrimaryColor;
pub use self::kinds::SecondaryColor;
pub use self::utils::mix;
```

这样调用者就可以清晰地使用crate提供的`PrimaryColor`和`SecondaryColor`结构，以及`mix`方法，而不用考虑crate内部复杂的模块结构。

---

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

---

另一种组织模块的方式是：

1. 在某个文件夹下创建`mod.rs`文件

2. `mod.rs`中声明的模块都将自动声明在与文件夹同名的模块下

3. 在文件夹中组织各模块的定义文件

---

Rust项目中可以使用来自外部（比如[crates.io](https://crates.io)）的crate。将外部crate引入自己项目的步骤是：

1. 在`Cargo.toml`中列出

2. 通过`use`引入项目的作用域

其实，标准库（std）也是外部crate，但Rust语言中一般包含标准库，所以不需要在`Cargo.toml`中列出，可以直接通过`use`引入。

引入外部crate：

```toml
[dependencies]
rand = "0.8.3"
```

---

Cargo有两个主要的配置：

- 运行`cargo build`时采用`dev`配置

- 运行`cargo build --release`时采用`release`配置

当项目的`Cargo.toml`中没有任何`[profile.*]`部分的时候，Cargo对每一种配置都采用默认设置，即：

```toml
[profile.dev]
opt-level = 0

[profile.release]
opt-level = 3
```

任何`Cargo.toml`中的定制都会覆盖默认的设置。

---

创建`crates.io`账号、发布crate等方法详见[将 crate 发布到 Crates.io](https://kaisery.github.io/trpl-zh-cn/ch14-02-publishing-to-crates-io.html)。

---

当crate持续增大之后，通常需要进一步拆分为多个库crate（多个package），这种时候就需要**工作空间**（workspaces）来管理多个包。

**工作空间**是一系列共享同一个`Cargo.lock`和输出目录的包。

创建工作空间的步骤为：

1. 创建项目文件夹

2. 在项目文件夹中创建`Cargo.toml`文件，该文件中不包含`[package]`之类的元信息，一个典型的`Cargo.toml`为：
   
   ```toml
   [workspace]
   
   members = [
       "adder",
   ]
   ```

3. 在项目文件夹中运行`cargo new`新建二进制crate（adder）

4. 可以运行`cargo build`构建工作空间。此时会在项目文件夹中生成`target`目录，而二进制crate中没有。

5. 继续创建新的包，则在项目文件夹中的`Cargo.toml`文件中增加新的成员：
   
   ```toml
   [workspace]
   
   members = [
       "adder",
       "add_one",
   ]
   ```

6. 和步骤3类似，可以通过`cargo new add_one --lib`新建一个库crate

7. 为了让二进制crate可以使用库crate的内容，需要为其添加依赖（adder/Cargo.toml）：
   
   ```toml
   [dependencies]
   add_one = { path = "../add_one" }
   ```

8. 在项目文件夹中运行`cargo build`构建工作空间

9. 通过`cargo run`运行项目，使用`-p`参数指定运行的二进制包：
   
   ```powershell
   cargo run -p adder
   ```

需要注意的是，各crate中对外部crate的依赖依然要在各crate的`Cargo.toml`中标注，但工作空间会保证整个项目都使用相同的外部crate。

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

Rust中的错误分为**可恢复的**（recoverable）和**不可恢复的**（unrecoverable）。

使用`panic!`处理不可恢复的错误：

```rust
panic!("crash and burn");
```

执行`panic!`宏会打印错误信息，展开并清理栈数据，然后退出。如果不需要清理数据而直接**终止**（abort）则在`Cargo.toml`中标注：

```toml
[profile.release]
panic = 'abort'
```

发生panic时，在**Windows**系统中可以通过`$env: RUST_BACKTRACE=1`设置环境变量来得到backtrace：

```powershell
$env: RUST_BACKTRACE=1; cargo run
```

---

使用`Result`处理可恢复的错误：

```rust
enum Result<T, E> {
    Ok(T),
    Err(E),
}
```

许多函数的返回值类型是`Result<T, E>`，它说明函数调用可能会成功，也可能会失败，我们需要分别处理：

```rust
use std::fs::File;

fn main() {
    let f = File::open("hello.txt");

    let f = match f {
        Ok(file) => file,
        Err(error) => panic!("Problem opening the file: {:?}", error),
    };
}
```

可以分析错误原因后更妥善地处理：

```rust
use std::io::ErrorKind;

let f = match f {
    Ok(file) => file,
    Err(error) => match error.kind() {
        ErrorKind::NotFound => match File::create("hello.txt") {
            Ok(fc) => fc,
            Err(e) => panic!("Problem creating the file: {:?}", e),
        },
        other_error => panic!("Problem opening the file: {:?}", other_error)
    }
};
```

或者用**闭包**书写：

```rust
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

`unwrap_or_else`方法在结果是`Ok`时返回其中的值，在产生错误时调用传入的闭包。

`unwrap`是很常用的辅助方法，它在结果是`Ok`时返回其中的值，在产生错误时调用`panic!`宏；如果需要传递更多的错误信息，可以使用`expect`方法：

```rust
use std::fs::File;

fn main() {
    let f = File::open("hello.txt").unwrap();
    let f = File::open("hello.txt").expect("Failed to open hello.txt");
}
```

有时也需要向上**传播错误**：

```rust
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

或者用`?`运算符书写：

```rust
use std::fs::File;
use std::io;
use std::io::Read;

fn read_username_from_file() -> Result<String, io::Error> {
    let mut s = String::new();    
    File::open("hello.txt")?.read_to_string(&mut s)?;
    Ok(s)
}
```

`Result`之后的`?`用于获取`Ok`中的值，或者将`Err`中的值作为函数返回值。

`Option`之后也可以使用`?`运算符，获取`Some`中的值，或是提前返回`None`：

```rust
fn last_char_of_first_line(text: &str) -> Option<char> {
    text.lines().next()?.chars().last()
}
```

---

使用`panic!`的情景一般有以下几种：

1. 编写示例、代码原型和测试。这类情况中，`panic!`一般作为错误处理的占位符，可以让程序更加简洁健壮，为后续处理留下标记。

2. 我们确信从逻辑上不会出现`Err`值。当我们确信在逻辑上不可能出现错误时，可以接受直接调用`unwrap`。

3. 可能会导致程序陷入有害状态。这种有害状态是非预期的行为，而此后的代码运行要求程序不处于这种有害状态，或者现阶段没有可行的手段来编码处理这类有害状态，则最好的处理方式就是`panic!`。（但如果这种有害状态是预期的行为，则最好考虑将失败情况向上传播，由调用者决定如何处理。）

---

有些时候我们可以利用一下编译器的类型检查机制来辅助完成一些验证工作。这种时候我们需要创建自定义类型：

```rust
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

`Guess`类型的关联函数`new`保证了所创建的`Guess`实例中，`value`值一定会在1到100之间。

对于可能会出现`panic!`的情况，开发者应当写入API文档中。

## 9. 泛型、Trait和生命周期

### 9.1 泛型

在结构体定义中使用泛型：

```rust
struct Point<T, U> {
    x: T,
    y: U,
}
```

在枚举定义中使用泛型：

```rust
enum Result<T, E> {
    Ok(T),
    Err(E),
}
```

在函数定义中使用泛型：

```rust
fn largest<T>(list: &[T]) -> T {
    //TODO
}
```

在方法定义中使用泛型：

```rust
impl<T> Point<T> {
    fn x(&self) -> &T {
        &self.x
    }
}
```

注意，方法或关联函数的定义中必须在`impl`后面声明`T`，这是为了避免和限制（constraint）的泛型类型混淆：

```rust
impl Point<f32> {
    fn distance_from_origin(&self) -> f32 {
        (self.x.powi(2) + self.y.powi(2)).sqrt()
    }
}
```

这段代码实现的`distance_from_origin`方法仅对`f32`类型的`Point`生效。

另外，方法中使用的泛型签名也未必总是和结构体自身的签名相同：

```rust
impl<X1, Y1> Point<X1, Y1> {
    fn mixup<X2, Y2>(self, other: Point<X2, Y2>) -> Point<X1, Y2> {
        Point {
            x: self.x,
            y: other.y,
        }
    }
}
```

### 9.2 Trait

*trait*类似于其他语言中的**接口**（interfaces），但不完全一样。

trait的定义：

```rust
pub trait Summary {
    fn summarize(&self) -> String;
}
```

`Summary`trait中要求实现一个`summarize`方法，并规定了方法签名。

trait的**命名规范**与结构体一致（Pascal命名规范）。

为trait增加默认实现：

```rust
pub trait Summary {
    fn summarize(&self) -> String {
        String::from("(Read more...)")
    }
}
```

trait中的默认实现也可以调用trait中的其他方法，即使它没有默认实现。

trait的实现：

```rust
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

使用trait的默认实现：

```rust
impl Summary for Tweet {}
```

需要注意，只有当**至少一个trait**或者**要实现trait的类型**位于crate的本地作用域时，才能为该类型实现trait。即，不能为外部类型实现外部trait。该限制被称为**相干性**（coherence），需要遵循**孤儿规则**（orphan rule）。

---

可以限定函数参数实现了某一trait：

```rust
pub fn notify(item: &impl Summary) {
    println!("Breaking news! {}", item.summarize());
}

//完整形式的trait bound
pub fn notify<T: Summary>(item: &T) {
    println!("Breaking news! {}", item.summarize());
}
```

从而调用该参数有关trait的实现。

第二种完整的trait bound适用于较复杂的场景：

```rust
//不使用trait bound
pub fn notify(item1: &impl Summary, item2: &impl Summary) {
}

//使用trait bound
pub fn notify<T: Summary>(item1: &T, item2: &T) {

}
```

有时候也需要同时满足多个trait：

```rust
pub fn notify(item: &(impl Summary + Display)) {

}

//trait bound
pub fn notify<T: Summary + Display>(item: &T) {

}
```

复杂的trait bound也可以使用`where`进行简化：

```rust
fn some_function<T: Display + Clone, U: Clone + Debug>(t: &T, u: &U) {

}

//使用where简化
fn some_funtion<T, U>(t: &T, u:&U)
    where T: Display + Clone,
          U: Clone + Debug
{

}
```

---

实现某一trait的类型也可以作为返回值：

```rust
fn return_summarizable() -> impl Summary {
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

当需要返回都实现了同一trait的不同类型时，就需要使用trait对象：

```rust
//TODO
```

通过trait bound可以有条件地实现泛型方法：

```rust
impl<T: Display + PartialOrd> Pair<T> {
}
```

也可以通过trait bound有条件地实现trait：

```rust
impl<T: Display> ToString for T {
}
```

这种对任何满足特定trait bound的类型都实现trait，被称为**blanket implementations**。

### 9.3 生命周期

**生命周期**（lifetimes）是一种特殊的泛型，它保障了引用的有效性。

Rust编译器具有**借用检查器**（borrow checker），它通过比较作用域来确保借用的有效性。

有效的引用（借用）：被引用的数据应当拥有比引用者更长的生命周期。

生命周期注解：

```rust
&i32        // 引用
&'a i32     // 带有显式生命周期的引用
&'a mut i32 // 带有显式生命周期的可变引用
```

使用生命周期注解：

```rust
fn longest<'a>(x: &'a str, y: &'a str) -> &'a str {
    if x.len() > y.len() {
        x
    } else {
        y
    }
}
```

泛型生命周期`'a`的具体生命周期等同于`x`和`y`的生命周期中较小的那个。

---

当需要定义包含引用的结构体时，就需要为每一个引用添加生命周期注解：

```rust
struct ImportantExcerpt<'a> {
    part: &'a str,
}
```

为这类结构体实现方法：

```rust
impl<'a> ImportantExcerpt<'a> {
    fn level(&self) -> i32 {
        3
    }
}
```

---

生命周期省略规则：

0. 函数或方法的参数的生命周期被称为**输入生命周期**（input lifetimes），返回值的生命周期被称为**输出生命周期**（output lifetimes）。

1. 每一个引用参数都有其自己的生命周期参数。

2. 如果只存在一个输入生命周期参数，则所有输出生命周期参数都使用该参数。

3. 如果输入生命周期参数中有一个参数是`&self`或`&mut self`，则所有输出生命周期参数都使用`self`的生命周期参数。

---

静态生命周期存活于整个程序期间：

```rust
let s: &'static str = "I have a static lifetime.";
```

## 10. 自动化测试

测试函数体通常执行三种操作：

1. 设置所需的数据或状态

2. 运行被测代码

3. 断言结果是否符合期望

测试有两个主要分类：

- **单元测试**（unit tests）：在隔离的环境中逐个测试模块或私有接口。

- **集成测试**（integration tests）：以外部代码测试公有接口。

---

Rust中的属性注解：

```rust
#[test]
fn it_works() {}
```

Rust中，单元测试的规范是在每个文件中创建包含测试函数的`tests`模块，并使用`cfg(test)`标注模块。`#[cfg(test)]`注解告诉Rust只在执行`cargo test`时才编译和运行测试代码，而在运行`cargo build`时不这么做：

```rust
#[cfg(test)]
mod tests {
    #[test]
    fn it_works() {
        assert_eq!(2 + 2, 4);
    }
}
```

在模块注解中，`cfg`属性代表configuration，注明该内容只应被包含在特定配置的选项中。

Cargo只会在使用`cargo test`运行测试时才编译测试模块，包括测试模块中可能存在的帮助函数，以及标注为`#[test]`的测试函数。

测试模块中可以通过`use super::*`将上一级模块中的所有项都引入测试模块的作用域，这样就可以根据需求对私有函数进行测试。

---

编写测试时，通常会使用断言宏：

```rust
assert!();//断言是否为true
assert_eq!();//断言是否相等
assert_ne!();//断言是否不等
```

可以向断言宏传递一个可选的失败信息参数；任何超过必须参数的部分都会传递给`format!`宏：

```rust
assert!(
    result.contains("Carol"),
    "Greeting did not contain name, value was '{}'",
    result
);
```

---

另一些情况下测试需要检查是否如期发生了`panic`，这就需要用到`should_panic`注解：

```rust
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

可以进一步提供panic信息：

```rust
#[should_panic(expected = "Guess value must be less than or equal to 100")]
```

---

还有一些情况下可能需要使用`Result<T, E>`编写测试：

```rust
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

这样就可以在函数体中使用`?`运算符了。但不要对这类测试使用`#[should_panic]`注解，而且也不要对`Result<T, E>`值使用`?`来断言，应当使用`assert!(value.is_err())`。

---

`cargo test`命令的参数根据分隔符`--`划分，之前的是传递给`cargo test`命令的参数，之后的是传递给生成的测试二进制文件的参数：

```powershell
cargo test --help #cargo test的帮助信息
cargo test -- --help #test二进制文件的帮助信息
```

`cargo test`默认并行地运行所有测试。

`cargo test -- --test-threads=1`限制仅使用一个线程运行所有测试。

`cargo test -- --show-output`显示成功测试的输出。（否则`println!`输出会被截获）

`cargo test test_fn_name`指定运行的测试。（过滤运行所有匹配的测试）

`cargo test -- --ignored`只运行属性注解为`#[ignore]`的测试。（默认不运行）

`cargo test -- --include-ignored`运行包括属性注解为`#[ignore]`在内的测试。

---

创建**集成测试**需要先创建一个*tests*目录（与*src*同级）。`Cargo`会将其中的每一个测试文件都当作单独的crate编译：

```rust
use adder;

#[test]
fn it_adds_two() {
    assert_eq!(4, adder::add_two(2));
}
```

*tests*目录中的文件不需要标注`#[cfg(test)]`。

可以通过`cargo test --test test_file_name`来运行某个集成测试文件中的所有测试。

由于`Cargo`默认会将*tests*中的每一个文件都当作集成测试文件，当需要在测试文件中构建一些公共模块时，可以通过创建子目录并添加`mod.rs`文件的形式来表明这是公共模块。

---

**文档测试**

在文档注释中增加的示例代码可以作为文档测试代码：

```rust
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

运行`cargo test`便会出现文档测试的部分。

---

最后需要注意的是，二进制crate（如`src/main.rs`）中定义的函数无法通过集成测试（无法访问到`main.rs`中的函数）。

## 11. 常规开发流程

Rust项目的常规开发流程：

1. 基本功能的实现

2. 重构以改进模块性

3. 错误处理

4. 测试驱动开发（TDD）

---

Rust中获取命令行参数可以通过`std::env::args`迭代器：

```rust
use std::env;

fn main() {
    let args: Vec<String> = env::args().collect();
    println!("{:?}", args);
}
```

---

重构和拆分模块一般按照如下顺序：

- 将程序拆分为`main.rs`和`lib.rs`，将程序逻辑放入`lib.rs`中。

- 当命令行解析逻辑较小时，可以保留在`main.rs`中。

- 当命令行解析开始变得复杂时，将其从`main.rs`提取到`lib.rs`中。

最终，`main`函数的职责为：

- 调用命令行解析逻辑

- 设置其他配置

- 调用`lib.rs`中的`run`函数

- 如果`run`函数可能返回错误，则处理该错误

即，`main.rs`处理程序运行，而`lib.rs`处理真正的业务逻辑。

---

测试驱动开发模式按照如下顺序：

1. 编写一个（失败的）测试，并运行它以确保失败的原因是你所期望的。

2. 编写或修改足够的代码来使新的测试通过。

3. 重构刚刚增加或修改的代码，并确保测试仍然能通过。

4. 重复以上步骤。

---

Rust中，通过`env::var("ENV_VARIABLE").is_err()`来获取环境变量是否被设置。

**Windows**中使用PowerShell设置环境变量：

```powershell
$env: ENV_VARIABLE=1;
```

---

重定向输出流到指定的文件中：

```powershell
cargo run > output.txt
```

---

将错误打印到标注错误流则使用`eprintln!`宏替代`println!`宏：

```rust
eprintln!("Some Problems");
```

## 12. 迭代器与闭包

### 12.1 闭包

Rust中的**闭包**（closures）是可以保存在变量中传递的匿名函数。不同于函数，闭包不要求在参数和返回值上注明类型，闭包还允许捕获调用者作用域中的值。

闭包的定义以一对竖线`|`开始，在`|`中指定闭包的参数，闭包体存放在之后的大括号内：

```rust
let expensive_closure = |num| {
    println!("calculating slowly...");
    thread::sleep(Duration::from_secs(2));
    num
};

expensive_closure(10);
```

调用闭包的地方通常离定义闭包的地方不远，所以编译器很容易推断参数和返回值的类型。

当闭包体只有一行时，甚至可以去掉大括号：

```rust
let add_one = |x| x + 1;
```

经常会将闭包存放在结构体、枚举或函数参数中，这时就需要使用泛型和trait bound：

```rust
struct Cacher<T>
where
    T: Fn(u32) -> u32,
{
    calculation: T,
    value: Option<u32>,
}
```

所有的闭包都实现了`Fn`、`FnMut`或`FnOnce`三个trait中的一个。（函数也实现了这三个trait，不需要捕获环境值得情况下也可以使用函数）

闭包可以捕获环境值：

```rust
fn main() {
    let x = 4;

    let equal_to_x = |z| z == x;

    let y = 4;

    assert!(equal_to_x(y));
}
```

但从环境中捕获值是会产生额外开销的。

闭包通过三种方式捕获环境，对应函数三种获取参数的方式：获取所有权、可变借用、不可变借用。

这三种捕获方式被编码为三个trait：

- `FnOnce`：消费周围环境中捕获的变量，因为所有权被移动进闭包，所以只能调用一次。

- `FnMut`：获取周围环境变量的可变借用。

- `Fn`：获取周围环境变量的不可变借用。

Rust会根据闭包对环境变量的使用方式自动推断需要实现的trait。

当需要强制闭包获取环境变量所有权时，可以在参数列表前使用`move`关键字：

```rust
fn main() {
    let x = vec![1, 2, 3];

    let equal_to_x = move |z| z == x;

    let y = vec![1, 2, 3];

    assert!(equal_to_x(y));
}
```

但需要注意，添加`move`关键字并不影响编译器推断闭包所需要实现的trait。

大部分情况下，当需要指定闭包的trait bound时都可以从`Fn`开始，然后根据编译器的提示决定是否需要`FnMut`或`FnOnce`。

### 12.2 迭代器

**迭代器**（iterator）负责遍历序列中的每一个元素并决定序列何时结束。

Rust中的迭代器是**惰性的**（lazy），直到使用前都不会产生效果。

```rust
let v1 = vec![1, 2, 3];

let v1_iter = v1.iter();

for val in v1_iter {
    println!("Got: {}", val);
}
```

所有的迭代器都实现了`Iterator`trait，这是一个定义在标准库中的trait：

```rust
pub trait Iterator {
    type Item;
    fn next(&mut self) -> Option<Self::Item>;
}
```

如果需要直接调用迭代器上的`next`方法，则需要迭代器本身是可变的，因为`next`方法消费（修改）了迭代器：

```rust
let v1 = vec![1, 2, 3];

let mut v1_iter = v1.iter();

assert_eq!(v1_iter.next(), Some(&1));
```

`for`循环不需要迭代器可变，这是因为`for`循环会获取迭代器的所有权并在后台使迭代器可变。

`iter`方法生成的是对象的不可变引用的迭代器；如果需要获取具备对象的所有权的迭代器，则可以调用`into_iter`方法；如果需要获取具备对象的可变引用的迭代器，则可以调用`iter_mut`方法。

---

`Iterator`trait中有一些标准库提供的实用方法，但其中有些方法调用了`next`方法而消耗了迭代器，这会使得调用之后迭代器不再能被继续使用，典型的例子是`sum`方法：

```rust
let v1 = vec![1, 2, 3];

let v1_iter = v1.iter();

let total: i32 = v1_iter.sum();
```

这类方法被称为**消耗适配器**（consuming adaptors）。

还有一类被称为**迭代适配器**（iterator adaptors）的方法，允许生成新的迭代器，典型的例子是使用`map`：

```rust
let v1 = vec![1, 2, 3];

let v2 = v1.iter().map(|x| x + 1).collect();
```

常用的迭代适配器还有`filter`，它接收一个使用迭代器中的项并返回布尔值的闭包，如果返回值为`true`则将项包含到新的迭代器中。

---

可以实现`Iterator`trait来创建自定义的迭代器，唯一要做的就是实现`next`方法：

```rust
struct Counter {
    count: u32,
}

impl Counter {
    fn new() -> Counter {
        Counter { count: 0}
    }
}

impl Iterator for Counter {
    type Item = u32;

    fn next(&mut self) -> Option<Self::Item> {
        if self.count < 5 {
            self. count += 1;
            Some(self.count)
        } else {
            None
        }
    }
}
```

## 13. 智能指针

**智能指针**（smart pointers）通常拥有数据的所有权，而引用只是借用数据。

Rust中，`String`和`Vec<T>`类型也都属于智能指针。

智能指针通常使用结构体实现，与常规结构体的区别在于，智能指针实现了`Deref`和`Drop`trait。`Deref`trait允许智能指针结构体实例表现得像引用一样；`Drop`trait允许自定义智能指针离开作用域时运行的代码。

标准库中其他常用的智能指针包括：

- `Box<T>`：用于在堆上分配数据

- `Rc<T>`：引用计数类型，数据可以有多个所有者

- `Ref<T>`和`RefMut<T>`，通过`RefCell<T>`访问

---

使用`Box<T>`在堆上存储一个`i32`：

```rust
let b = Box::new(5);
println!("b = {b}");
```

在堆上存储`i32`并不常见，更适合`Box`的地方是创建递归类型：

```rust
enum List {
    Cons(i32, Box<List>),
    Nil,
}

use crate::List::{Cons, Nil};

fn main() {
    let list = Cons(1, Box::new(Cons(2, Box::new(Cons(3, Box::new(Nil))))));
}
```

`Deref`trait提供了**Deref强制转换**（deref coercions），使得对智能指针的引用传递就如同对其内数据本身的引用传递一样：

```rust
fn hello(name: &str) {
    println!("Hello, {}!", name);
}

fn main() {
    let m = String::from("Rust");
    hello(&m);
}
```

可变引用也有相应的Deref强制转换。

总的来说，有三种情况会发生Deref强制转换：

- 当`T: Deref<Target=U>`时，从`&T`转换到`&U`

- 当`T: DerefMut<Target=U>`时，从`&mut T`转换到`&mut U`

- 当`T: Deref<Target=U>`时，从`&mut T`转换到`&U`

Rust不允许主动调用`Drop`方法，但可以通过标准库提供的`std::mem::drop`来提前释放：

```rust
fn main() {
    let s = String::from("some data");
    drop(s);
}
```

---

`Rc<T>`是**引用计数**（referece counting）智能指针，当不再有引用时就会被清理。

使用`Rc<T>`共享数据：

```rust
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

每次调用`Rc::clone`都会增加相应的引用计数。

`Rc<T>`中是不可变引用，如果需要可变性，可以通过`RefCell<T>`和内部可变性模式。

---

**内部可变性**（interior mutability）是指，一个值可以在方法内部修改自身，而在值方法的外部则不可以。内部可变性不违反借用规则，它将在运行时检查借用规则。

`RefCell<T>`代表其中数据唯一的所有权，运行时会依此检查借用规则。

例如当一个需要实现的trait方法获取的是不可变引用：

```rust
pub trait Messenger {
    fn send(&self, msg: &str);
}
```

为了能在特定场合下对不可变引用中的数据进行修改，就可以使用`RefCell<T>`：

```rust
use std::cell::RefCell;

struct MockMessenger {
    sent_messages: RefCell<Vec<String>>,
}

impl Messenger for MockMessenger {
    fn send(&self, message: &str) {
        self.sent_messages.borrow_mut().push(String::from(message));
    }
}
```

`RefCell<T>`的`borrow`方法返回`Ref<T>`类型的智能指针，`borrow_mut`方法返回`RefMut<T>`类型的智能指针。

`RefCell<T>`通过记录当前活动的`Ref<T>`和`RefMut<T>`实现了在运行时遵守借用规则。

另外，结合`Rc<T>`和`RefCell<T>`可以实现多个可变数据所有者：

```rust
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

但如果使用这类方法修改引用对象则可能产生循环引用，进而造成内存泄漏。

解决内存泄漏有几种方式：

- 重新组织数据结构，使得一部分引用拥有所有权而另一部分没有。

- 通过自动化测试、代码评审、软件开发最佳实践等方式使其最小化。

- 通过`Weak<T>`传递**弱引用**（weak reference），当强引用归零时自动清理。

调用`Rc::clone`会增加强引用，而调用`Rc::downgrad`则是传递弱引用。强引用共享了`Rc<T>`实例的所有权，而弱引用没有所有权。为了确保使用弱引用时的有效性，可以通过`upgrade`方法判断，其返回值是`Option<Rc<T>>`。

```rust
use std::cell::RefCell;
use std::rc::{Rc, Weak};

#[derive(Debug)]
struct Node {
    value: i32,
    parent: RefCell<Weak<Node>>,
    children: RefCell<Vec<Rc<Node>>>,
}
```

---

选择`Box<T>`，`Rc<T>`或`RefCell<T>`的依据：

- `Rc<T>`允许相同数据有多个所有者；`Box<T>`和`RefCell<T>`是单一所有者。

- `Box<T>`允许编译期执行不可变或可变借用检查；`Rc<T>`仅允许编译期执行不可变借用检查；`RefCell<T>`允许在运行时执行不可变或可变借用检查。

- `RefCell<T>`可以在即使自身不可变的情况下，修改其内部的值。

## 14. 并发
