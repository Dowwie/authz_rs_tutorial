+++
chapter = true
date = "2017-02-12T10:00:00-00:00"
icon = "<b>1. </b>"
prev = "/"
next = "/02.lib.rs"
title = "Project Crate"
weight = 10
+++

### <center> Project:  authz </center>

**authz** is a *library* that performs permission-based authorization.  It is
a port from [Yosai](https://www.github.com/YosaiProject/yosai).

**rust-authz** is the name of the [github project](https://www.github.com/Dowwie/rust-authz) but the name of the [crate](https://crates.io/crates/authz) is simply **authz**.

### Project Layout

The **authz** crate consists of the following standardized directory structure:
```yaml
 authz
   ├── benches
   │   └── benchmarks.rs
   ├── Cargo.toml
   └── src
       ├── authz.rs
       └── lib.rs
```
This is a very basic crate layout.  Within it are two directories:  *benches* and *src*.
These directories are named using reserved words, recognized by cargo, that specify
the function its contents provide for the crate, namely benchmarking tests and
core source repository.

For more information about crate layout, [visit here](https://doc.rust-lang.org/book/crates-and-modules.html)

### authz is a Library

As mentioned, authz is a *library*.  Specifically, authz compiles into a dynamic C library
and a Rust library.  The C library is used to establish a common interface from rust
to other languages that can speak C, such as Python, using a common foreign function
interface (cffi).  The Rust library (rlib) is imported by native Rust applications.

The way we specify that authz is to compile into a cdylib and rlib is within
the [lib] section of the `Cargo.toml` file:
```yaml
[lib]
crate-type = ["cdylib", "rlib"]
```
