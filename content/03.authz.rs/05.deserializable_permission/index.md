+++
chapter = true
date = "2017-02-12T10:00:00-00:00"
icon = "<b>3.5. </b>"
prev = "/03.authz.rs/04.permission_struct"
next = "/03.authz.rs/06.permission_impl"
title = "(De)Serializable Permission"
weight = 80
+++

## <center>A (De)Serializable Permission</center>
<hr/>

```rust
#[derive(Debug, PartialEq, Serialize, Deserialize)]
pub struct Permission {

    #[serde(default = "default_part")]
    domain: String,

    #[serde(default = "default_hash_part")]
    actions: HashSet<String>,

    #[serde(default = "default_hash_part")]
    targets: HashSet<String>
}
```

## <center>Attributes</center>

Notice how Permission and its three fields are each decorated by a line beginning with "#[...]".  This hash-bracket syntax denotes the use of Rust [Attributes](https://doc.rust-lang.org/book/attributes.html). An Attribute is used in a declarative style of programming, decorating its target below.


```rust
#[derive(Debug, PartialEq, Serialize, Deserialize)]
pub struct Permission {
    ...
}
```

The *derive* Attribute automatically generates traits for data structures. This particular declaration derives 4 traits for Permission: Debug, PartialEq, Serialize, and Deserialize.


### <center>Custom Attributes</center>

```rust
#[serde(default = "default_part")]
domain: String,

#[serde(default = "default_hash_part")]
actions: HashSet<String>,

#[serde(default = "default_hash_part")]
targets: HashSet<String>
```

These are customized Attributes in that they are not sourced from Rust's standard library of Attributes. Rust supports customized Attributes only under one condition:  that the custom Attribute(s) is for "something" being derived.  In this example, our custom *#[serde(...)]* Attribute is allowed by the Rust compiler because the Attribute is decorating a field that is part of a struct that is having a Deserialize trait derived for it.  The Deserialize trait makes use of the *#[serde(...)]* attribute when it needs to obtain a default value for the decorated field.  In this example, we are declaring a function to call for a default field value.  For instance, the "default_hash_part" function is called by serde during deserialization when no value is available for the *actions* field.


## <center>Default-Value Functions for Serde Deserialization</center>

Serde calls the following functions to obtain default values for fields unpresented in serialized messages.

```rust
fn default_part() -> String {
    "*".to_string()
}
```
The function *default_part* is a very basic function that converts a string primitive to a String and returns the String.  There are [two types of strings](http://rustbyexample.com/std/str.html) in Rust:  ***&str*** and ***String***.  *"*"* is a string primitive, represented in Rust as *&str*.  We convert this primitive to a String type by calling the to_string() method of the str primitive.

```rust
fn default_hash_part() -> HashSet<String> {
    ["*"].into_iter().map(|s| s.to_string()).collect()
}
```
The function *default_hash_part* returns a HashSet (of Strings).
["*"] is a single-string array, represented as ***[&str; 1]***.  It is converted *into* an iterator that has, as part of its api, a *map* function that executes a string conversion closure for each element of the iterator.  Once the mapping finishes, the collect function gathers all of the elements of the iterator together and stores the Strings into a HashSet<String>.
