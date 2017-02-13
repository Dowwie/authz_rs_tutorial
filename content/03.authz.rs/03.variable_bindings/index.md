+++
chapter = true
date = "2017-02-12T10:00:00-00:00"
icon = "<b>3.3. </b>"
prev = "/03.authz.rs/02.global_statics"
next = "/03.authz.rs/04.permission_struct"
title = "Variable Bindings"
weight = 60
+++

## <center>static vs let</center>
<hr/>
Recall from the prior slide our creation of two global, static variables:

```rust
static PART_DELIMETER: &'static str = ":";
static SUBPART_DELIMETER: &'static str = ",";
```

In this program, our one-character string ":" is bound to a name, PART_DELIMETER, is given a static lifetime, and is assigned a global scope within the module.

Notice how the keyword ***let***, used for *variable binding*, isn't used here. We need a globally scoped variable and have no use for patterns, so we use ***static***. *let* is used to introduce a binding between a value and a *pattern*.  *let* must be used within, and is scoped to, a function.  For instance, the following requires that it be declared within a function:
```rust
let PART_DELIMETER: &'static str = ":";
```

*PART_DELIMETER* in this example is a ***pattern***.  The *pattern* can simply be binding a name to a value, which is all that *static* can do, but a pattern has the flexibility to enable more elaborate functionality, such as binding multiple values at once.  An example of this multi-variable binding is within the *Permission::new* constructor, [documented](/03.authz.rs/08.permission_constructor) in this tutorial.

Note:  Statics *can* be used within the scope of a function but they are restricted by rules.  However, details about this are beyond the scope of this tutorial.
