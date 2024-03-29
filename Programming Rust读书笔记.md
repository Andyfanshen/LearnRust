# Rust的基础使用
1. 通常使用rustup来安装Rust。rustup是一个管理Rust安装、更新的工具。使用rustup安装Rust会自动安装cargo、rustc和rustdoc工具。
2. cargo是Rust的编译管理工具、包管理工具以及通用工具。使用Cargo可以创建新的工程、构建和运行代码，以及管理依赖库。
3. rustc是Rust的编译器。我们通常使用Cargo来调度编译器，必要时也可以手动调用。
4. rustdoc是Rust的文档工具。如果在源码中以正确的形式书写注释，那么rustdoc就可以自动构建出格式美观的HTML文档。和rustc一样，我们通常使用Cargo来调度文档工具。
5. `debug_assert!`是一种特殊的断言，仅在debug模式下生效。
# Rust的基础类型
Rust的内存和线程安全性依赖于其健壮的类型系统，而Rust的灵活性则源于其泛型和特性（trait）。
1. Rust的基础数据类型都是围绕硬件中的固定宽度数据类型设计的，这满足了绝大多数应用的需求且可以运行得非常快，如果需要任意精度的类型，可以考虑使用`num`包。
2. 只有ASCII字符具有字节字面值（`b'x'`），结果为一个`u8`数值。
3. 也可以使用两位十六进制来强调这是一个ASCII字符的字节字面值（`b'\x1b'`），而不是单纯的数值`27u8`。
4. `(-4).abs()`将会报错，这是由于自动类型推断发生在检查完所有的方法调用之后，所以此处必须明确指明使用哪一种类型或是指明使用哪一种方法（此外，注意方法调用的优先级高于一元前缀运算符）。
5. 整型的方法通常可以分为四类：检查（`checked_div`）、覆盖（`wrapping_mul`）、填充（`saturating_sub`）和溢出（`overflowing_add`）。
6. 覆盖类返回结果对数据类型取模的值。有符号类型的取模可能会产生负值。
7. 填充类返回最接近数学结果的值。结果会被“钳制”在数据类型的范围内。填充类方法没有除法、求余和移位。
8. 溢出类返回一对结果`(result, overflowed)`，前者与覆盖类的计算一致，后者则为一个描述计算是否溢出的布尔类型。
9. 浮点类型的字面值最多由四部分构成：整数、小数（可以只有一个小数点）、指数和类型后缀。只有整数部分是必须要有的，但后三者至少存在一种。
10. 其他类型一般不能转换为布尔类型，但是布尔类型可以通过`as`转为整型，且`false`值转为`0`，`true`值转为`1`。
11. 虽然布尔类型只需要1bit表示，但Rust中使用了1byte存储，因而可以使用指针指向它。
12. 字符类型采用Unicode字符，每个字符为一个32bit值。
13. 字符串类型则采用UTF-8编码，这是一种变长编码。但对string或者&str使用`len()`方法时，得到的是其字节长度而不是字符数量。
14. 可以用形如`'\xHH'`的格式表示一个ASCII字符，用形如`'\u{HHHHHH}'`的格式表示一个Unicode字符。
15. 字符类型可以通过`as`转为整型类型，如果是转为小于32bit的整型，则截取掉高位部分。
16. 与之相反的是，只有`u8`整型可以通过`as`转为字符类型，但也可以通过`std::char::from_u32`函数实现。
17. Rust允许在并列的元素后尾随一个逗号，例如元组`("lonely hearts",)`，这有利于元素的增删，也有利于区分单元素的元组类型和括号表达式。
18. 可以使用`[N;V]`来快速初始化数组。
19. 虽然数组的方法通常都在切片上，但Rust在检索方法时会隐式地将数组引用转为切片，因此可以直接在数组上使用。
20. 由于slice引用既可以用于数组，也可以用于vector，因而适用于对数组或vector操作的函数。
21. 如果字符串字面值仅包含换行，则换行和次行开头的空格也会包含在字符串中；但如果行末以反斜杠结尾，则换行和次行开头的空格都会被舍弃。
22. 当需要使用包含复杂结构的原始字符串时，可以通过`r`+`#`+`""`+`#`的结构囊括起来，其中双引号前后的`#`数量保持一致。例如`r###"This raw string started with 'r###"'. Therefore it does not end until we reach a quote mark ('"') followed immediately by three pound signs ('###')."###`。
23. 虽然类型`&mut str`是存在的，但几乎没有什么能用的方法，只有`make_ascii_uppercase`和`make_ascii_lowercase`可以使用。
24. 对str字面值使用`to_string()`方法和`to_owned()`方法具有一样的效果。
# 所有权和移动语义
1. Vector不支持直接移动其中的某个值，因为这通常需要额外的信息来记录那些位置变为未定义的。一般通过借用来访问Vector中的值，如果需要移动，则可以考虑使用`pop()`、`swap_remove()`、`replace()`等方法。
2. 对于Option类型的实例，如果需要取出其中的值并转移所有权，则可以使用`take()`方法，转移后将在原变量处留下`None`值。
3. 整型、浮点型、字符类型、布尔类型等在内的基础类型都是Copy类型，由它们组成的元组类型和定长数组也是Copy类型。
4. 只有可以按位复制的类型可以是Copy类型，File类型不是Copy类型，MutexGuard类型也不是Copy类型。只有成员都是Copy类型，复合类型才可以是Copy类型（默认不是，需要添加属性`#[derive(Copy, Clone)]`）。
5. 一般来说，如果某种类型在释放时需要特殊操作，则不会是Copy类型。例如，Vec类型需要释放各元素占用的内存，File类型需要关闭文件，MutexGuard需要给互斥器解锁。
6. 引用计数指针`Rc<T>`所指向的值不能改变，这避免了产生循环引用。“内部可变性”是一类特殊的机制，允许修改那些本身可变的值。
7. 不可变借用的类型为`&T`，可以同时存在多个，且不可变借用的赋值是通过拷贝实现的；可变借用的类型为`&mut T`，同时只能存在一个，且可变借用没有Copy Trait。
8. 与C++不同的是，Rust中的引用是可以改动的，初始指向A的引用可以修改为指向B，而C++中的引用一旦初始化完成便不可修改。
9. 与`.`操作符相似，Rust中的比较操作符也可以自动解析任意数量的引用。
10. `std::ptr::eq`可用于判断两个引用是否指向同一个内存地址。
11. 对于临时创建的数据的引用，数据生命周期的长度取决于引用的使用：如果该引用立即赋予一个独立变量或成员变量，则生命周期与变量相同；否则生命周期将在该语句结束时终止。
12. 类型定义中出现的引用类型需要显式标注它的生命周期。
13. 即使是`i32`也是有生命周期的，但像`i32`和`String`这样的数据通常是`'static`的，因此`Vec<i32>`也具有独立的生命周期；但`Vec<&'a i32>`就具有确定的生命周期，必须包含在`'a`之内。
14. 当存在多个生命周期可用时，编译器会选择使用最小的那个。
# 表达式
1. Rust是表达式语言，这一点与C系语言不同。Rust中`if`和`match`语法是可以产生值并赋予其他变量的。
2. 虽然可以在语句块中定义函数，但仍不能直接使用当前语句块中的变量。捕获当前范围内的变量需要使用闭包。
3. `match`语法会保证分支覆盖所有可能，并具有相同的表达式（值）类型。
4. `if let`是一种简写语法，`if let`完全可以由`match`代替，通常只有单值匹配时才使用`if let`语法。
5. Rust中的循环也是一种表达式，但`for`循环和`while`循环的值都是没有太大意义的`()`，只有`loop`循环可以指定表达式值。
6. 范围操作符`..`会产生一个范围类型，范围类型由两个字段组成，一个是开始字段，另一个是结束字段。范围类型是一种可迭代类型（实现了`std::iter::IntoIterator`特性）。
7. 与移动语义保持一致，`for`循环会消耗掉（移动）所迭代的对象。如果希望保持原位，可以改为对其引用的迭代。
8. `loop`循环可以通过`break`语法指定其表达式值，`break`语法也只能用在循环语法中。
9. 循环语法也可以标注生命周期，此时，循环内部的`break`语法可以指定生命周期来跳出循环，`continue`语法也可以指定生命周期来进入下一轮循环。（`break`语法可以同时指定生命周期和循环表达式的值）
10. 部分特殊的表达式并不能指定表达式值（例如死循环、`panic!()`等），对于这类特殊的表达式，它们使用一种特殊的值类型：`!`，该类型意味着表达式不会返回任何值，是发散函数。
11. 当需要指定泛型函数的特化类型时，可以通过`turbofish`语法实现：`return Vec::<i32>::with_capacity(1000);`
12. 与C语言不同，Rust中的按位操作优先级高于比较运算。
13. 与C语言不同，Rust不支持链式赋值。
14. 从浮点类型向整型转换会发生向零截取（`-1.99 as i32` 为 -1）。
15. 闭包既可以在定义时指明参数类型，也可以由编译器根据上下文内容推断（函数则必须指明类型）。闭包如果有返回值，则主体部分必须由语句块构成（语句块才能形成表达式并返回值）。
# 错误处理
1. 当发生`panic`时，Rust会进行堆栈展开（unwind）或是中断进程，默认行为是unwind。堆栈展开会根据栈中顺序释放临时变量和过程参数（内存被释放，打开的文件被关闭，调用相关的drop方法）。
2. `panic`是有确定行为的运行时异常，它是安全的，并不会引发内存泄漏之类的问题。发生`panic`意味着程序未按期望的方式运行，出现了需要修复的逻辑错误。
3. `panic`是逐线程的，这意味着某条线程发生`panic`时，其他线程仍然可以正常运作。
4. 堆栈展开也可以通过`std::panic::catch_unwind()`捕获，这在编写一些交由C或C++调用的Rust代码时很有必要。
5. 当执行drop方法时若再次发生`panic`，一般认为发生了致命错误，Rust不再进行unwind，而是直接中断进程。
6. Rust的`panic`行为是可以通过编译器指令控制的，如果指定直接中断进程，则Rust不再在发生`panic`时进行unwind（一定程度上也可以减小编译出来的文件大小）。
7. Rust的另一种错误处理方式与其他语言中的try/catch语法相似，是`Result<T, E>`类型。
8. 可以通过`err.source()`去溯源上一级错误。
9. `?`操作符用于错误传播。当发生错误时会立即返回包含错误的`Result`值。
10. 当需要自定义错误类型时，可以考虑引入`thiserror`包。
11. 标准库的错误类型都满足trait对象`Box<dyn std::error::Error + Send + Sync + 'static>`。但实际开发时应当首选像`anyhow`这样的包。
12. 标准库的错误类型具有`downcast_ref::<ErrorType>()`方法，可以从一般性的错误类型细化。
# 包和模块
1. 对于依赖中的包，Cargo调用rustc编译时会指定参数`--crate-type lib`来避免rustc寻找`main()`函数，转为生成`.rlib`文件。`.rlib`文件中包含编译好的代码，可用于生成二进制文件或其他的`.rlib`文件。
2. 对于一般的程序，Cargo调用rustc编译时会指定参数`--crate-type bin`来生成目标平台的二进制可执行文件。
3. 截至目前（2023年初），Rust一共有三个版次（2015、2018、2021），不同版次之间有语言特性上的区别，但Rust语言兼容性保证，已编译的代码在使用时不受版次的影响（2015版次的项目可以正常使用2018版次的包）。对于包维护者而言，不用担心后续的版次会影响已发布的包，只有当包维护者觉得有必要使用新版次的语言特性时，才需要去更新自己的包。
4. 尽可能使用最新的rustc版本，旧版本的代码可以通过`cargo fix`更新。
5. `Cargo.toml`中可以设置不同情形下的项目配置，例如`cargo build`指令将采用`[profile.dev]`中的配置。一般使用默认配置即可，但如果需要对项目进行性能分析，则需要同时启用`debug`和`release`的配置，可以采用如下的配置方案：
```toml
[profile.release]
debug = true # 在release下启用debug
```
6. 在某个模块中，可以使用`pub`关键字来暴露所定义的内容，但`pub(crate)`则仅限于当前crate中全局可见，且无法被用于其他crate。此外，还可以使用`pub(super)`来使所有父类模块可见，或是使用`pub(in <path>)`来指定特定的模块可见。
7. 当通过`mod spores;`声明模块时，Rust将检查`spores.rs`文件和`spores/mod.rs`文件，但两者只能存在一个。当某个子模块（例如`stems`）仍然包含子模块时，则可以在`stems.rs`的同目录下创建`stems`文件夹，并在`stems.rs`中声明子模块（例如`pub mod xylem;`）。
8. 建议导入类型、trait和模块，但仍通过相对路径调用函数、常量和成员变量。
9. Rust中，子模块并不会自动继承父模块中的内容，所以子模块中仍需要显式地导入（`use super::AminoAcid;`）。
10. 合理使用`super`和`crate`关键字，用`crate`描述的路径更方便文件的迁移。
11. 为了避免内部模块和外部包的重名冲突，可以使用绝对路径标志`::`，它总是指向外部包，例如`use ::image::Pixels;`，而指向内部模块则可以使用`self`关键字，例如`use self::image::Sampler;`。
12. 使用`pub use`可以将导入的内容作为当前模块的公开成员。
13. 对于常量的借用，请使用静态变量。如果需要持有大规模的数据，请使用静态变量。
14. 虽然Rust中可以定义可变静态类型，但由于违反线程安全规则而无法在一般情况下使用。
15. 可以用属性来装饰各类内容来给编译器提供一些额外信息，常用的属性包括`#[allow]`、`#[cfg]`、`#[inline]`和`#[test]`等。例如`#[allow(unused_must_use)]`。
16. 有些属性可以用于模块，有些则不能。如果要用于整个包，则需要将属性放在`main.rs`或`lib.rs`的最上方，且使用`#![]`语法。
17. `#![feature]`属性将启用一些不稳定的语言特性或是库特性（仅可以在nightly版本中使用）。
18. 对于应当抛出错误的测试用例，可以使用`#[should_panic]`属性。
19. 对于有单元测试的内容，`cargo test`会编译两遍代码，第一遍不含单元测试内容，第二遍则包含单元测试内容。这可以确保编译警告或错误提示与单元测试内容无关。
20. 当有大量与测试相关的代码时，可以通过`#[cfg(test)]`属性构建一个专用的测试模块。
21. Rust中的单元测试默认是并发执行的，可以通过`cargo test -- --test-threads 1`来强制单线程执行。
22. 另一种测试编写方式是集成测试。集成测试将所有测试代码编写在一个单独的*tests*目录中，编译时作为一个独立的包链接到库包上。集成测试更接近于用户对库包的访问方式。`cargo test`指令将同时运行单元测试和集成测试。
23. 通过`cargo doc --no-deps --open`可以生成文档并打开，`no-deps`意味着仅生成自身文档，不生成依赖包的文档。
24. 当Rust编译器发现文档注释的内容，会自动将其当做使用了`#[doc]`属性。即`/// Some commets.`等同于`#[doc = "Some comments."]`。
25. 与上一条相似的，`//!`开头的注释内容会被当作`#![doc]`属性处理。
26. 文档注释采用Markdown语法，且可以使用Rust的对象路径（例如`leaves::Leaf`）而无需使用URL，Cargo编译时会检查路径并替换为文档链接。
27.  可以通过`#[doc(alias="...")]`提供别名用于文档中的搜索。
28. 文档注释中的代码会被当作文档测试。文档中的代码应当比一般文本多四个空格的缩进，且上下需与一般文本隔开一行。如果某些代码不需要在文档中显示（初始化操作），可以在那行代码开头加上`#`和一个空格。
29. 编译器会将文档注释中的代码编译为独立的可运行包并链接到原包中，因此需要注意被测试内容的可见性。
30. 如果希望编译器仅编译文档中的代码而不实际运行，则可以使用Markdown语法将该部分代码标记为`no_run`文本；如果不希望编译器编译，则标记为`ignore`文本；如果代码是其他语言的，则可以标记为对应语言。
31. 对于来自git仓库的依赖包，可以通过指定URL和版本号获取：`image = { git = "https://github.com/Piston/image.git", rev = "528f19c" }`
32. 对于本地文件，也可以直接指定路径：`image = { path = "vendor/image" }`
33. 语义版本控制规则：对于`0.x`版本（x不为0），会采用`0.x`开头的兼容版本；对于`x.0`及以上的版本（x不为0），会采用主版本为x的兼容版本（例如`2.17.99`）。
34. 可以用一些运算符来控制版本的范围，例如`=0.10.0`或是`>1.0.5 <1.1.9`。
35. 第一次构建工程时会创建`Cargo.lock`文件，后续的构建会采用`Cargo.lock`文件中确定的各依赖库的版本。如果需要更新，则可以使用`cargo update`指令来手动更新，使所有依赖库更新到兼容的最新版本。
36. 上传可执行项目时应当同时上传`Cargo.lock`文件；上传库包时不需要上传`Cargo.lock`，除非是一个共享库包（即`.dll`、`.dylib`或`.so`文件）。
37. 上传包之前需要先通过`cargo package`指令将项目打包为`.crate`文件。
38. 当一个庞大的项目中包含多个包时，可以通过工作空间（workspace）来共享不同包之间相同的依赖，节省磁盘空间和编译时间。
39. 工作空间在根目录的`Cargo.toml`中配置：
```TOML
[workspace]
members = ["fern_sim", "fern_img", "fern_video"]
```
40. 合并为工作空间后，删去各包中的`Cargo.lock`文件和target文件夹。
41. 使用`cargo build --workspace`指令可以构建当前工作空间下的全部包。
42. 使用`Github`托管项目时，可以使用Travis CI来完成自动测试。
# 结构体
1. 结构体的命名规范：结构体采用`CamelCase`，字段采用`snake_case`。
2. 在一些树形或图形结构的方法签名中，使用`self: Rc<Self>`有时是更好的选择。
3. Rust中的方法和关联函数是可以在多处分离定义的，这有一些好处：使结构体的定义更加简洁，很容易看到由哪些成员字段构成；`impl`模块不仅可以用于结构体方法和关联函数的定义，也可用于枚举类型和原生类型的定义（这也是Rust中不使用“对象”这一概念的原因）；`impl`语法也可以很好地和trait语法结合。
4. 关联常数可以提供只与类型有关的常值，定义和使用如下：
``` rust
impl Vector2{
    const ZERO: Vector2 = Vector2 { x: 0.0, y: 0.0 };
    const UNIT: Vector2 = Vector2 { x: 1.0, y: 0.0 };
}

let scaled = Vector2::UNIT.scaled_by(2.0);
```
5. 当需要内部可变性时，可通过`Cell`类型的`get()`和`get(value)`方法实现，或是通过`RefCell`类型的`borrow()`类方法实现。
# 枚举类型和模式
1. 枚举类型的大小采用能容纳其对应整型范围的最小值，只有一个枚举值的枚举类型占用空间为0，超过一个枚举值时根据最大值确定（255以内是1byte，以此类推）。
2. 枚举类型可以通过`#[repr]`属性覆写其内存表示。
3. 枚举类型可以通过`as`转为对应整型，但整型无法通过`as`转为对应枚举类型。可以手动实现转型，也可以使用`enum_primitive`包的宏实现。
4. 模式匹配可以通过`ref`来借用原值（而不是move），模式匹配也可以通过`&`来匹配引用。
5. 匹配守卫，可以在模式匹配时增加条件判断来约束匹配。
6. `@`匹配用于同时提供匹配约束和获取匹配值的情况，例如`rect @ Shape::Rect(..) => {render(&rect)}`。
# 特性和泛型
1. 使用特性中的方法或关联函数必须将特性引入作用域中（prelude除外）。
2. Rust中的trait对象必须显式使用引用指定（例如`let writer: &mut dyn Write = &mut buf;`）。
3. 在内存中，trait对象是一个胖指针，由一个指向值的指针和一个指向类型的指针构成。
4. 生命周期参数采用单引号（`'`）+ 小写字母，而类型参数则使用大写字母，生命周期参数写在类型参数的前面。
5. 当需要更小的编译结果时，选择使用trait对象；而当需要更快的运行速度（编译优化）时，选择使用泛型，因为泛型在编译期特化后会提供更多信息。
6. 可以为符合约束的所有类型实现某一trait：`impl<W: Write> WriteHtml for W {...}`
7. 类似于子接口，Rust中也有子特征（subtrait），定义方式为：`trait Creature: Visible {...}`
8. 子特征不继承超特征的内容（子特征实质上是描述一种约束关系），实现子特征的类型必须实现相应的超特征（supertrait）。
9. 使用完全限定语法调用trait方法：`<str as ToString>::to_string("hello")`
10. 定义trait时，可以为其添加关联类型。实现具有关联类型的trait时，必须指定关联类型。
11. 可以约束trait中的关联类型，例如`where I: Iterator<Item=String>`或是`where I: Iterator, I::Item: Debug`。
12. 也可以约束trait对象的关联类型，例如`fn dump(iter: &mut dyn Iterator<Item=String>){...}`
13. 泛型特征提供了一种“孤儿规则“的特例，即只需要特征的一项类型参数定义于当前包，就可以实现该泛型特征。举例：`impl Mul<WindowSize> for f64`以及`impl<T> Mul<WindowSize> for Vec<T>`。
14. `impl Trait`是一类特殊的签名方式，多用于返回值描述中，即不具体指定返回值类型而是要求返回值实现了某一特征。举例：`fn cyclical_zip(v: Vec<u8>, u: Vec<u8>) -> impl Iterator<Item=u8>{...}`
15. 使用`impl Trait`好处在于不再受具体返回值类型的限制，可以更加灵活地更改和迭代原有的函数/方法。但`impl Trait`是静态分发的，所以依赖于运行时的逻辑无法通过编译（例如match语法）。
16. 也可以在泛型参数中使用`impl Trait`，例如`fn print<T: Display>(val: T)`等价于`fn print(val: impl Display)`。
17. 特征也可以定义关联常数，例如
```rust
trait Greet {
    const GRETTING: &'static str = "Hello";
    fn greet(&self) -> String;
}
```
18. 特征中的关联常数可以只声明不定义：`const ZERO: Self;`
# 操作符重载
1. Rust中的比较运算重载需要实现`PartialEq`特征，之所以称为`PartialEq`，是由于IEEE标准不满足数学上对相等性的“自反性”的要求，具体来说，IEEE规定了`NaN`必须和其他所有值都不相等（包括自身）。另外，任何与`NaN`的比较运算都返回`false`。
2. Rust中几乎所有实现了`PartialEq`的类型也都实现了`Eq`，只有`f32`和`f64`是例外。
3. 如果只需要比较自定义类型中各成员字段之间是否相等，则自定义类型的`PartialEq`和`Eq`特征都可以用属性自动实现：`#[derive(Eq, PartialEq)]`。
4. `PartialOrd`是对`PartialEq`的扩展，要求实现`partial_cmp()`方法，该方法返回一个`Option<Ordering>`类型，其中`Ordering`是一个枚举类型，包含`Less`、`Equal`和`Greater`三种值。基础类型中，只有浮点数之间的比较可能会返回`None`。
5. `Ord`是对`PartialOrd`的扩展，要求实现`cmp()`方法，该方法返回一个`Ordering`类型。几乎所有实现类了`PartialOrd`的基础类型也都实现`Ord`，除了`f32`和`f64`。
6. `std::cmp::Reverse`是一种特殊的包装类型，它对所有实现了`Ord`的类型都生效，作用是反转`Ord`的排序。
7. `a[i]`是`*a.index(i)`的语法简写形式，实现`std::ops::Index`特征或`std::ops::IndexMut`特征即可使用。
# 通用特征
1. Rust中常用的Trait分为三种类型：语言扩展型（`Drop`，`Deref`，`From`等）、标记型（`Sized`，`Copy`等）和公共词汇型（`AsRef`，`TryFrom`等）。
2. `Drop`的的调用是由外及内的，例如`String`内部使用`Vec<u8>`来存储字符，所以`String`本身的`drop`并不需要处理内存释放。
3. `Sized`特征由Rust自动实现，用户无法为某个类型实现`Sized`特征。`Sized`特征一般用于特征约束。一些常见的`unsized`类型包括字符切片、数组切片和特征对象。
4. Rust默认泛型参数需要实现`Sized`特征，当类型不必是`Sized`时，可以使用`T: ?Sized`约束，这可以使得泛型方法可以传入切片和特征对象。
5. 结构体类型的最后一个字段也可以是`unsized`的（此时该结构体也是`unsized`的）。
6. 由于`Clone`特征要求`clone()`函数的返回值为`Self`类型，而函数的返回值必须是`Sized`的，因而`Clone`特征隐式地含有对类型的`Sized`约束。
7. 