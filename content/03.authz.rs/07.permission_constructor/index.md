+++
chapter = true
date = "2017-02-12T10:00:00-00:00"
icon = "<b>3.7. </b>"
prev = "/03.authz.rs/06.permission_impl"
next = "/03.authz.rs/08.fn_init_parts"
title = "Constructor Pattern"
weight = 110
+++

## <center>The Constructor Pattern</center>
<hr/>

```rust
pub fn new(wildcard_perm: &str) -> Permission {
    let (domain, actions, targets) = Permission::init_parts(wildcard_perm);
    let perm = Permission {
        domain: domain,
        actions: actions,
        targets: targets,
    };
    perm
}
```

*new* is an associated function that implements a constructor pattern.  The first assignment illustrates what is known as a "destructuring let".  Here, the call to *init_parts* returns a 3-element [tuple](https://doc.rust-lang.org/book/primitive-types.html#tuples) that is unpacked and each of its three elements is bound to a corresponding name:  domain, actions, and targets.  These three variables are then used as arguments for a new Permission instance, which is returned in the last line.  The keyword "return" is implied and unnecessary in Rust if the last line of a function isn't terminated with a semicolon.
