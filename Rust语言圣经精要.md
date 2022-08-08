# Rust语言圣经精要

Rust语言系列第二弹，《Rust语言圣经》（也就是Rust Course）内容精要。

## 1. Cargo项目管理

以包（Package）为单位的项目中，会伴随两个核心文件：`Cargo.toml`和`Cargo.lock`。

- `Cargo.toml`是`cargo`特有的**项目数据描述文件**，存储了项目的元配置信息。

- `Cargo.lock`是`cargo`工具根据`toml`文件生成的**项目依赖详细清单**，一般不需要修改。

> 一般来说，当项目是一个可运行的程序时，可以上传`Cargo.lock`；如果只是一个依赖库项目，则将`Cargo.lock`添加到`.gitignore`中（不上传）。

---

在`Cargo.toml`中，有三种
