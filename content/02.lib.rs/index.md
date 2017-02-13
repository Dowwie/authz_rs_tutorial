+++
chapter = true
date = "2017-02-12T10:00:00-00:00"
icon = "<b>2. </b>"
prev = "/01.project_crate"
next = "/03.authz.rs"
title = "lib.rs"
weight = 20
+++

## <center> lib.rs </center>

```rust
#[macro_use]
extern crate serde_derive;

extern crate serde;
extern crate serde_json;

pub mod authz;
pub use authz::{is_permitted_from_str, is_permitted_from_perm, perms_from_buffer, Permission};
```


The *authz* crate compiles to a library.

**/src/lib.rs** is considered the **crate root** of the project.

The contents of the authz crate are referenced in another project through lib.rs.  That is, lib.rs serves as a gateway to authz.

[Reference Documentation](https://doc.rust-lang.org/book/conditional-compilation.html)

```rust
extern crate X
```
extern crate is Rust's import mechanism


```rust
#[macro_use]
extern crate serde_derive;
```
These two lines state to import the serde_derive crate *and* its macros. *#[macro_use]* decorates *extern crate serde_derive* to indicate that macros are to be included with the import of the serde_derive crate

```rust
pub mod authz
```

instructs Rust to look for */src/authz.rs* or */src/authz/mod.rs*, and make it available for public use.  If you were to mistakenly include BOTH of these module files for the same module name, the Rust compiler will raise an exception during compilation, stating that it found both files and hinting that you remove one in order to eliminate ambiguity.


```rust
pub use authz::{is_permitted_from_str, is_permitted_from_perm, ...}
```

exposes functions from the authz module for public use.  Exposing a function from lib.rs reduces the knowledge required by a consumer to use that function.  If the **use** statement were omitted, a developer would need to reference functions using a convention such as *authz::authz::is_permitted_from_str*.
