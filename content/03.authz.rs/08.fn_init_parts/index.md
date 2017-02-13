+++
chapter = true
date = "2017-02-12T10:00:00-00:00"
icon = "<b>3.8. </b>"
prev = "/03.authz.rs/07.permission_constructor"
next = "/03.authz.rs/09.fn_part_from_str"
title = "fn init_parts"
weight = 120
+++

## <center>fn init_parts</center>
<hr/>

```rust
fn init_parts(wildcard_perm: &str) -> (String, HashSet<String>, HashSet<String>) {
    let mut iter = wildcard_perm.split(PART_DELIMETER).map(str::trim);

    let domain = match iter.next() {
        Some("") | Some("*") | None => String::from("*"),
        Some(domain) => String::from(domain),
    };
    let actions = Permission::part_from_str(iter.next());
    let targets = Permission::part_from_str(iter.next());

    (domain, actions, targets)
}
```
*init_parts* turns a string representation of a permission into the 3 fields
required by the Permission struct, using default values when necessary.

## Chaining Calls

*init_parts* is provided a string primitive (&str), *wildcard_perm*.

```rust
    let mut iter = wildcard_perm.split(PART_DELIMETER).map(str::trim);
```

This statement is a chain of calls.  The first call splits up the wildcard_perm string using the str::split method.  The string is split using the static global, *PART_DELIMETER*, as its delimiter.  The *split* method returns a [std::str::Split type](https://doc.rust-lang.org/std/str/struct.Split.html), which implements the Iterator trait (among others).  With this Split type, the map method is called.  *map* takes a closure, in this case str::trim, creates an std::iter::Map iterator, and then calls the closure on each element of the iterator, ultimately returning a mutable Map type.

Rather than chaining calls, you could perform the operations in steps.  The only disadvantage of this approach is that it's less concise.  The performance and memory consumption remains the same as with the chain:
```rust
    let split_perm = wildcard_perm.split(PART_DELIMETER);
    let mut iter = split_perm.map(str::trim);
```

Notice how the iterator isn't collected into a collection type, such as a Vector. In this case, collecting wasn't necessary and doing so would have created unnecessary overhead.

We declar iter as a mutable value by using the 'mut' prefix, as presented.  Later in the function are *iter.next()* calls.  *iter.next()* is a *mutable borrow* operation in that it updates the state of iter through a reference (borrow). To facilitate updates to iter from *iter.next()* calls, iter must be declared mutable.

### Obtaining Domain, Actions, and Targets

The rest of the function uses the elements of the iter Map to derive values that are bound to three local names:  domain, actions, and targets.  *init_parts* returns these three bindings in a tuple.

```rust
    let domain = match iter.next() {
        Some("") | Some("*") | None => String::from("*"),
        Some(domain) => String::from(domain),
    };
```

Domain is always the first element of the permission string.  We match on the next (first) element of the Map, iter.  If the element's value is empty, "*", or doesn't exist, then match returns a String of "*".  Otherwise, match returns a Stringified version of the element.

Notice how a few values are wrapped by ***Some*** or our use of ***None***?  Some and None are the supported values of an [Option enum](https://doc.rust-lang.org/std/option/enum.Option.html).  iter.next() returns an "Optionized value" and so we match on that.

```rust
    let actions = Permission::part_from_str(iter.next());
    let targets = Permission::part_from_str(iter.next());
```
Finally, actions and targets are bound to values obtained from calls made to the function *part_from_str*, using the remaining two elements of iter as arguments.  *part_from_str* is discussed in the next section.
