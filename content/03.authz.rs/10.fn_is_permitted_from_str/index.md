+++
chapter = true
date = "2017-02-12T10:00:00-00:00"
icon = "<b>3.10. </b>"
prev = "/03.authz.rs/09.fn_part_from_str"
next = "/03.authz.rs/11.perms_from_buffer"
title = "fn is_permitted_from_str"
weight = 140
+++

## <center>fn is_permitted_from_str</center>
<hr/>

```rust
pub fn is_permitted_from_str<'a, I>(required_perm: &str, assigned_perms: I) -> i32
    where I: IntoIterator<Item = &'a str>
{
    let required_permission = Permission::new(&required_perm);

    for assigned in assigned_perms {
        let assigned_permission = Permission::new(assigned);
        if assigned_permission.implies_from_perm(&required_permission) {
            return 1;
        }
    }
    return 0;
}
```
*is_permitted_from_str* determines whether a required permission, expressed as a borrowed string, is satisfied by permissions assigned to a user, expressed as an iterator of borrowed strings.

Notice how *is_permitted_from_str* is proceeded by ***<'a, I>***.  This states that *is_permitted_from_str* is a function that is generic over **lifetime 'a** and **type I**, where **I** implements IntoIterator with a **str of lifetime 'a** as its *associated type* Item.

### <center>Lifetimes</center>

***'<name>*** is the convention used to define a [lifetime parameter](https://doc.rust-lang.org/book/lifetimes.html), which in this case we have simply named **a**.  Rust forces programmers to take ownership of how long their objects will exist in memory when the compiler cannot infer object lifetime on its own.  We use the single apostrophe followed by the name of the lifetime when we scope our activities within a lifetime.

When a type reference is annotated with a lifetime parameter (in this case, 'a), we are declaring that the reference (borrow) is to remain valid (live) for *at least* the lifetime of 'a, if not longer.  In other words, we are declaring the minimum lifespan of a variable in memory.  The compiler's borrow checker keeps objects alive in memory for only as long as it needs to.

**is_permitted_from_str** requires the use of a lifetime parameter because it borrows values in the iterator passed to it by a calling function, specifically items that of type &str.  Everything in Rust has a lifetime.  The reason that you don't see them everywhere is that they are often inferred by the compiler.  However, *borrows* required explicit annotation of lifetimes because the compiler cannot infer an appropriate lifetime for them. The compiler cannot infer what lifetime to assign these borrowed values.  In this example, we tell the compiler that our borrowed strings ought to live for the lifetime of the function.  A lifetime is declared at the block-level.  Here, our block is the function, *is_permitted_from_str*.


### <center>Generic Over Type I</center>
The where clause constrains (bounds) what type is accepted for I:  a type that implements IntoIterator and whose items are strings.  This approach is used so that the function that calls *is_permitted_from_str* doesn't have to convert its dataset into a collection prior to passing it.
