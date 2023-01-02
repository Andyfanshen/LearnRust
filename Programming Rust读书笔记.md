# Rust的基础使用
1. 通常使用rustup来安装Rust。rustup是一个管理Rust安装、更新的工具。使用rustup安装Rust会自动安装cargo、rustc和rustdoc工具。
2. cargo是Rust的编译管理工具、包管理工具以及通用工具。使用Cargo可以创建新的工程、构建和运行代码，以及管理依赖库。
3. rustc是Rust的编译器。我们通常使用Cargo来调度编译器，必要时也可以手动调用。
4. rustdoc是Rust的文档工具。如果在源码中以正确的形式书写注释，那么rustdoc就可以自动构建出格式美观的HTML文档。和rustc一样，我们通常使用Cargo来调度文档工具。
5. `debug_assert!`是一种特殊的断言，仅在debug模式下生效。
6. `raw string`保留原始字符串内容，以`r`开头，紧跟0~n个`#`字符，中间是用双引号包裹的任意unicode字符序列，然后以同样数量的`#`字符结束。
# Rust的基础类型
Rust的内存和线程安全性依赖于其健壮的类型系统，而Rust的灵活性则源于其泛型和特性（trait）。