+++
chapter = true
date = "2017-02-12T10:00:00-00:00"
icon = "<b>3.4. </b>"
prev = "/03.authz.rs/03.variable_bindings"
next = "/03.authz.rs/05.deserializable_permission"
title = "Permission Struct"
weight = 70
+++

## <center>The Permission Struct</center>
<hr/>

A [struct](https://doc.rust-lang.org/book/structs.html) is the construct used in Rust to create more complex data types.  It allows a developer to manage state and associated behaviors, similar to the role of a class in object oriented languages.

A Permission type is modeled as a 3-field struct:
```rust
pub struct Permission {
    pub domain: String,
    pub actions: HashSet<String>,
    pub targets: HashSet<String>
}
```

Suppose we were to model a permission that describes a bank account withdrawal from a particular bank account: *bankaccount:withdrawal:account12345*.  In this example, our Permission's domain is *bankaccount* , actions is a HashSet (of Strings) containing the *withdraw* String, and targets is a HashSet (of Strings) containing the *account12345* String.

This struct definition would have sufficed had we not needed to be able to (de)serialize instances of a Permission.  However, since Permissions are serializable, we must update the struct definition accordingly.
