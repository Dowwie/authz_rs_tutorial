+++
chapter = true
date = "2017-02-12T10:00:00-00:00"
icon = "<b>3.12 </b>"
prev = "/03.authz.rs/12.perms_from_buffer"
next = "/"
title = "Unit Testing"
weight = 160
+++

## <center>Unit Testing</center>
<hr/>

```rust
#[cfg(test)]
mod test {
    use serde_json;
    use authz::{Permission, is_permitted_from_str, is_permitted_from_perm, perms_from_buffer};
    use std::collections::HashSet;

    #[test]
    fn test_new_permission() {
        ...
    }
```

In Rust, your unit tests exist within the module that they test, as a sub-module.  This approach is a departure from how unit testing is implemented in other programming languages -- within modules separate from their test targets.  This practice is entirely safe and sound for the following two reasons:

1. Unit tests are distinguished from the rest of a module  by decorating the section of unit testing code with a special *#[cfg(test)]* attribute and encapsulating all of the tests within a "test" sub-module: *mod test {..}*.  This attribute allows the compiler to ignore the section of code it decorates unless the compiler is explicitly instructed to run tests, such as by invoking "cargo test" from the command line.

2. Unit tests test public *and* private objects.  Private objects are not accessible from outside the module that they exist but they are accessible, through imports, by descendant sub-modules (such as the test sub-module).

```rust
    #[test]
    fn test_new_permission() {
        ...
    }
```
The *#[test]* attribute is used to tell the compiler that the function it decorates is a test function and is to be run as a test unit.
