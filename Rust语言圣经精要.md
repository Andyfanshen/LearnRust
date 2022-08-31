# Rust语言圣经精要

Rust语言系列第二弹，[《Rust语言圣经》](https://course.rs/about-book.html)（也就是Rust Course）内容精要。

## 1. Cargo项目管理

以包（Package）为单位的项目中，会伴随两个核心文件：`Cargo.toml`和`Cargo.lock`。

- `Cargo.toml`是`cargo`特有的**项目数据描述文件**，存储了项目的元配置信息。

- `Cargo.lock`是`cargo`工具根据`toml`文件生成的**项目依赖详细清单**，一般不需要修改。

> 一般来说，当项目是一个可运行的程序时，可以上传`Cargo.lock`；如果只是一个依赖库项目，则将`Cargo.lock`添加到`.gitignore`中（不上传）。

---

在`Cargo.toml`中，有三种写法描述项目依赖：

- 基于Rust官方仓库`crates.io`，通过版本说明描述

- 基于项目源代码的git仓库地址，通过URL来描述

- 基于本地项目的绝对路径或者相对路径，通过类Unix模式来描述

```toml
[dependencies]
rand = "0.3"
hammer = { version = "0.5.0" }
color = { git = "https://github.com/bjz/color-rs" }
geometry = { path = "crates/geometry" }
```

---

外部依赖包通常需要从`crates.io`下载，境内下载缓慢可以通过换源解决，详情参考[下载依赖太慢了？ - Rust语言圣经(Rust Course)](https://course.rs/first-try/slowly-downloading.html)

## 2. 调试

有时候需要按条件编译特定代码块：

```rust
if cfg!(debug_assertions) {
    eprintln!("debug: {:?} -> {:?}", record, fields);
}
```

上述代码仅在`debug`模式下生效，它会将`debug`信息打印到标准错误输出中。

## 3. 开发

### 3.1 命名规范

| 条目                            | 惯例                           |
|:----------------------------- |:---------------------------- |
| 包 Crates                      | unclear                      |
| 模块 Modules                    | snake_case                   |
| 类型 Types                      | UpperCamelCase               |
| 特征 Traits                     | UpperCamelCase               |
| 枚举 Enumerations               | UpperCamelCase               |
| 结构体 Structs                   | UpperCamelCase               |
| 函数 Functions                  | snake_case                   |
| 方法 Methods                    | snake_case                   |
| 通用构造器 General constructors    | new or with_more_details     |
| 转换构造器 Conversion constructors | from_some_other_type         |
| 宏 Macros                      | snake_case!                  |
| 局部变量 Local variables          | snake_case                   |
| 静态类型 Statics                  | SCREAMING_SNAKE_CASE         |
| 常量 Constants                  | SCREAMING_SNAKE_CASE         |
| 类型参数 Type parameters          | UpperCamelCase，通常使用一个大写字母: T |
| 生命周期 Lifetimes                | 通常使用小写字母: 'a，'de，'src        |
| Features                      | unclear                      |

---

当类型发生转换时，使用`as_`、`to_`、`into_`这样的前缀来区分不同的操作：

- `as_`的开销为0，调用前后对变量都是借用。

- `to_`的开销昂贵，所有权可能发生变化，但都执行了相当复杂和昂贵的操作。

- `into_`总会获取所有权，性能开销需要看具体实现。

---

读访问器（Getter）**直接使用**对应的成员数据名称命名：

```rust
pub struct S {
    first: First,
    second: Second,
}

impl S {
    pub fn first(&self) -> &First {
        &self.first
    }

    pub fn first_mut(&mut self) -> &mut First {
        &mut self.first
    }
}
```

只在有且仅有**一个值**能被获取时，才可以使用`get_`前缀命名，例如`Cell::get`。

---

集合上的**方法**，如果返回迭代器，需要遵循命名规范，使用`iter`、`iter_mut`和`into_iter`，返回的类型也应使用`Iter`、`IterMut`和`IntoIter`：

```rust
fn iter(&self) -> Iter // Iterator<Item = &U>
fn iter_mut(&mut self) -> IterMut // Iterator<Item = &mut U>
fn into_iter(self) -> IntoIter //Iterator<Item = U>
```

非同构性（遍历类型与集合类型不一致）的数据集合通常根据所需类型单独命名。

---

其他命名规范：同类型的命名请采用一致的词序。

### 3.2 更多的语言特性

1. 解构赋值可以用在元组、切片和结构体上：
   
   ```rust
   struct S{
       e: i32,
   }
   
   fn main() {
       let (a, b, c, d, e);
       (a, b) = (1, 2);
       [c, .., d, _] = [1, 2, 3, 4, 5];
       S { e, .. } = S { e: 5 };
       assert_eq!([1, 2, 1, 4, 5], [a, b, c, d, e]);
   }
   ```

2. 得益于编译器的NLL优化，借用检查的作用域为从声明到最后一次使用之间，故可以写出这样不违反借用检查的代码：
   
   ```rust
   let r1 = &s;
   let r2 = &s;
   println!("{} {}", r1, r2);
   
   let r3 = &mut s;
   println!("{}", r3);
   ```

3. 通过字符串转义可以直接输出ASCII和Unicode字符：
   
   ```rust
   let byte_escape = "I'm writing \x52\x75\x73\x74!";
   let unicode_codepoint = "\u{211D}";
   let raw_str = "hello \\x52\\x75\\x73\\x74";
   let raw_str = r"Escapes don't work here: \x3F \u{211D}"; //忽略所有的转义
   let raw_str = r#"And then I said: "There is no escape!""#; //忽略一组“”
   ```

4. 

### 3.3 语言特性的常见QA

1. 何时使用可变变量（`mut`修饰）？
   
   答：当需要变量值可变的灵活性，又需要避免分配内存或拷贝数据带来的额外开销时。

2. 变量和常量之间的主要差异？
   
   答：常量不可使用`mut`修饰；常量始终不可变；常量使用`const`关键字声明；常量的声明必须标注类型。

3. 何处使用常量？
   
   答：多出代码需要共享的不可变值（例如$\pi$）。

4. 何时使用变量遮蔽（shadowing）?
   
   答：当作用域中不需要使用之前的变量（但仍想使用同一个变量名），或是需要给同名变量换一种数据类型时。

5. 为什么浮点数不能用作`HashMap`的`Key`？
   
   答：`HashMap`的`Key`需要实现`std::cmp::Eq`trait，而浮点数只实现了`std::cmp::PartialEq`trait。

6. 使用浮点数时需要注意什么？
   
   答：避免测试浮点数的相等性；避免产生未定义的结果。

7. 如果产生数学上未定义的结果会发生什么？
   
   答：会返回一个`NaN`，可以通过方法`is_nan`判断。

8. 如何打印小数点后2位小数？
   
   答：`println!("{:.2}", number);`。

9. 怎样区分表达式和语句？
   
   答：表达式有返回值（包括空返回值`()`），语句没有返回值。

10. 函数一定有返回值吗？
    
    答：对于绝大多数情况，是的（包括空返回值`()`，这被称为`单元类型`）；但也有一类特殊的函数永不返回，它们被称为发散函数（diverge function）。

11. 所有权的规则是什么？
    
    答：1.Rust中每一个值都有一个所有者变量；2.一个值只能有一个所有者；3.所有者离开作用域时，值将被丢弃。

12. 使用`=`（赋值号），何时发生拷贝，何时发生移动（转移所有权）？
    
    答：对于实现了`Copy`trait的数据类型，会在赋值时采用拷贝的方式；否则将发生所有权转移。

13. 哪些类型实现了`Copy`trait？
    
    答：1.所有基础数据类型；2.基础数据类型构成的元组；3.不可变引用。

14. 借用的规则是什么？
    
    答：1.同一时刻，或是拥有一个可变借用，或是拥有任意数量的不可变借用；2.引用必须有效。

15. 字符串类型（String）为什么不能通过下标访问？
    
    答：字符串的底层是采用UTF-8编码的`[u8]`数组，直接访问字节可能会得到不完整的信息。

16. 单元结构体（Unit-like Struct）的作用是什么？
    
    答：当需要定义特定行为，但不关心类型内容时，可以使用单元结构体。

17. 数组类型和数组切片的类型签名？
    
    答：数组类型是`[T; n]`，而数组切片是`[T]`。

18. 循环容器与使用迭代器的对应关系？
    
    答：
    
    | 使用方法                          | 等价方法                                              | 所有权   |
    | ----------------------------- | ------------------------------------------------- | ----- |
    | `for item in collection`      | `for item in IntoIterator::into_iter(collection)` | 所有权转移 |
    | `for item in &collection`     | `for item in collection.iter()`                   | 不可变借用 |
    | `for item in &mut collection` | `for item in collection.iter_mut()`               | 可变借用  |
    
    

19. 

### 3.4 常用标准库方法和函数

1. 整型溢出可使用这些方法处理：
   
   - 使用`wrapping_`方法按照补码循环溢出规则处理，例如`wrapping_add`。
   
   - 使用`checked_`方法在发生溢出时返回`None`值。
   
   - 使用`overflowing_`方法返回该值和指示是否溢出的布尔值。
   
   - 使用`saturating_`方法使返回值钳制在最小值和最大值之间。

2. 一些常用的编译器属性标记：
   
   - `#![allow(unused_variables)]`：忽略掉检查未使用的变量。
   
   - `#[repr(align(64))]`：强制内存对齐。

3. 一些常用的宏指令：
   
   - `todo!`：标记代码尚未实现，运行至该处将会引发错误。
   - `format!`：格式化字符串。
   - `dbg!`：（在标准错误输出`stderr`中）打印表达式的文件名、行号、求值结果等信息。

4. 

### 3.5 常用第三方库方法和函数

1. `num`库中提供了有理数和复数类型。
