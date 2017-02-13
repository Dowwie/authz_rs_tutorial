+++
chapter = true
date = "2017-02-12T10:00:00-00:00"
icon = "<b>3.9. </b>"
prev = "/03.authz.rs/08.fn_init_parts"
next = "/03.authz.rs/10.fn_is_permitted_from_str"
title = "fn part_from_str"
weight = 130
+++

## <center>fn part_from_str</center>
<hr/>

```rust
fn part_from_str(s: Option<&str>) -> HashSet<String> {
    let mut set = HashSet::new();
    match s {
        Some("") | None => {
            set.insert(String::from("*"));
        }
        Some(s) => {
            let mut set = HashSet::new();
            for rule in s.split(SUBPART_DELIMETER).map(str::trim) {
                set.insert(String::from(rule));
            }
        }
    }
    set
}
```
*part_from_str* is used to break a string into pieces and return those pieces in a HashSet, using a default wildcard asterisk when necessary.

*part_from_str* splits up a permission, using a SUBPART_DELIMETER, and returns the split parts as values of a HashSet.  A HashSet type is used to eliminate possible duplicate values and to take advantage of set operations during comparisons.

Argument **s** is an Option, so we match on Some value or None.
