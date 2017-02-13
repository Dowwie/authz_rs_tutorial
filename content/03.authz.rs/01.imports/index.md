+++
chapter = true
date = "2017-02-12T10:00:00-00:00"
icon = "<b>3.1. </b>"
prev = "/03.authz.rs"
next = "/03.authz.rs/02.global_statics"
title = "Imports"
weight = 40
+++

## <center>Imports</center>
<hr/>

### Crates

```rust
extern crate serde;
extern crate serde_json;
```
The **serde** crate is used for serialization and deserialization.  We import the main library and its json subsidiary with these lines:


### Data Structures

```rust
use std::str;
use std::error::Error;
use std::collections::HashSet;
```

*use crate::module::etc* is the standard convention to import specific "objects" from a crate/module
