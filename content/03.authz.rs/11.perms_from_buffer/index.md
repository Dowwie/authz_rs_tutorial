+++
chapter = true
date = "2017-02-12T10:00:00-00:00"
icon = "<b>3.11. </b>"
prev = "/03.authz.rs/10.fn_is_permitted_from_str"
next = "/03.authz.rs/12.unit_testing"
title = "fn perms_from_buffer"
weight = 150
+++

## <center>fn perms_from_buffer</center>
<hr/>

```rust
pub fn perms_from_buffer(serialized_perms: &[u8]) -> Result<Vec<Permission>, Box<Error>> {
    let result = try!(serde_json::from_slice(serialized_perms));
    Ok(result)

```

*perms_from_buffer* de-serializes JSON to a Vector of Permissions. It borrows serialized_perms (a slice of bytes) and returns a Result that is either a [Vector](https://doc.rust-lang.org/book/vectors.html) of Permissions or a Boxed Error.

## Byte Slices

Notice how this function borrows *serialized_perms*.  *serialized_perms* is a slice of bytes whose type is expressed in Rust as **&[u8]**. *serialized_perms* is in the form of a byte slice, rather than a string, because the JSON is stored in a particular datastore as bytes and consequently received from a datastore as bytes. No prior string conversion is necessary prior to handing the JSON over to the *serde_json* library for de-serialization because *serde_json* offers a *from_slice* function that can read byte slices.

## Error Handling

### The try! macro

```rust
let result = try!(serde_json::from_slice(serialized_perms));
```
*try!* is a helper [macro](https://doc.rust-lang.org/beta/book/macros.html) that reduces boilerplate code involved with calling a procedure and handling any errors that may arise from the call.  In the event that the code executed within a *try!* block raises an error, *try!* will either propagate *that* error or an error of your liking up the call stack without resuming execution of any other code within *perms_from_buffer*.  Because of the early return, *try!* can only be used in functions that return a Result type.  We address this requirement with our return type:  *Result<Vec<Permission>, Box<Error>>*

In the event that *let result = serde_json::from_slice(serialized_perms)* doesn't raise an error, the *pattern*, named 'result', is bound to the value returned by the function call: a Vector of Permissions.


### Boxes

When an error arises, *serde_json::from_slice* returns a Result containing a *boxed* error.

Suppose that we declared *x: Box<Error>*. **x** is referred to as a "boxed Error". Specifically, it is an Error [trait](https://doc.rust-lang.org/book/error-handling.html#the-error-trait) object allocated to space on the heap.  In Rust, a trait object cannot be *passed* around by its owner in its native form because a trait object is [unsized](https://doc.rust-lang.org/nightly/book/unsized-types.html).  Instead, we often box a trait object and then move it in its box.


### Results

```rust
Result<Vec<Permission>, Box<Error>>
```
*Result* is an [enumerated type](https://doc.rust-lang.org/std/result/index.html) that can take one of two forms:  a success (Ok) or failure (Err).  So, our call to *serde_json::from_slice* returns a Result type that will either be a Result::Ok(x), where x is a Vector of Permissions, or a Result::Err(e) where e is a boxed error.
