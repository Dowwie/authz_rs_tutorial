+++
chapter = true
date = "2017-02-12T10:00:00-00:00"
icon = "<b>3. </b>"
next = "/03.authz.rs/01.imports"
prev = "/03.authz.rs/11.perms_from_buffer"
title = "authz.rs"
weight = 30
+++

### <center> authz.rs </center>

```rust
extern crate serde;
extern crate serde_json;

use std::str;
use std::error::Error;
use std::collections::HashSet;

static PART_DELIMETER: &'static str = ":";
static SUBPART_DELIMETER: &'static str = ",";

#[derive(Debug, PartialEq, Serialize, Deserialize)]
pub struct Permission {

    #[serde(default = "default_part")]
    domain: String,

    #[serde(default = "default_hash_part")]
    actions: HashSet<String>,

    #[serde(default = "default_hash_part")]
    targets: HashSet<String>
}

fn default_part() -> String {
    "*".to_string()
}

fn default_hash_part() -> HashSet<String> {
    ["*"].into_iter().map(|s| s.to_string()).collect()
}

impl<'a> Permission {
    pub fn new(wildcard_perm: &str) -> Permission {
        let (domain, actions, targets) = Permission::init_parts(wildcard_perm);
        let perm = Permission {
            domain: domain,
            actions: actions,
            targets: targets,
        };
        perm
    }

    fn part_from_str(s: Option<&str>) -> HashSet<String> {
        match s {
            Some("") | None => {
                let mut set = HashSet::new();
                set.insert(String::from("*"));
                set
            }
            Some(s) => {
                let mut set = HashSet::new();
                for rule in s.split(SUBPART_DELIMETER).map(str::trim) {
                    set.insert(String::from(rule));
                }
                set
            }
        }
    }

    fn init_parts(wildcard_perm: &str) -> (String, HashSet<String>,
                                           HashSet<String>) {
        let iter = wildcard_perm.split(PART_DELIMETER).map(str::trim);

        let domain = match iter.next() {
            Some("") | Some("*") | None => String::from("*"),
            Some(domain) => String::from(domain),
        };
        let actions = Permission::part_from_str(iter.next());
        let targets = Permission::part_from_str(iter.next());

        (domain, actions, targets)
    }

    pub fn implies_from_str(&self, wildcard_permission: &str) -> bool {
        let permission = Permission::new(wildcard_permission);
        self.implies_from_perm(&permission)
    }

    pub fn implies_from_perm(&self, permission: &Permission) -> bool {
        if self.domain != "*" {
            if self.domain != permission.domain {
                return false;
            }
        }

        if !self.actions.contains("*") {
            if !&self.actions.is_superset(&permission.actions) {
                return false;
            }
        }

        if !self.targets.contains("*") {
            if !&self.targets.is_superset(&permission.targets) {
                return false;
            }
        }
        return true;
    }
}

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

pub fn is_permitted_from_perm(required_perm: &str,
                              assigned_perms: Vec<Permission>) -> i32 {
    let required_permission = Permission::new(required_perm);

    for assigned in assigned_perms {
        if assigned.implies_from_perm(&required_permission) {
            return 1;
        }
    }
    return 0;
}

pub fn perms_from_buffer(serialized_perms: &[u8]) ->
        Result<Vec<Permission>, Box<Error>> {
    let result = try!(serde_json::from_slice(serialized_perms));
    Ok(result)
}

#[cfg(test)]
mod test {
    use serde_json;
    use authz::{Permission, is_permitted_from_str,
                is_permitted_from_perm, perms_from_buffer};
    use std::collections::HashSet;

    #[test]
    fn test_new_permission() {

        struct Perm<'a> {
            wildcard_perm: &'a str,
            domain: &'a str,
            actions: Vec<&'a str>,
            targets: Vec<&'a str>,
        }

        let tests = [Perm { wildcard_perm: "",
                            domain: "*",
                            actions: vec!["*"],
                            targets: vec!["*"],},
                     Perm { wildcard_perm: "domain1",
                            domain: "domain1",
                            actions: vec!["*"],
                            targets: vec!["*"],},
                     Perm { wildcard_perm: "domain1:action1",
                            domain: "domain1",
                            actions: vec!["action1"],
                            targets: vec!["*"], },
                     Perm { wildcard_perm: ":action1, action2, action3",
                            domain: "*",
                            actions: vec!["action1", "action2", "action3"],
                            targets: vec!["*"],},
                     Perm { wildcard_perm: "domain1:action1, action2:target1, target2",
                            domain: "domain1",
                            actions: vec!["action1", "action2"],
                            targets: vec!["target1", "target2"], }];

        for test in tests.iter() {
            let perm: Permission = Permission::new(test.wildcard_perm);

            let expected_actions: HashSet<String> =
                test.actions.iter().map(|x| x.to_string()).collect();
            let expected_targets: HashSet<String> =
                test.targets.iter().map(|x| x.to_string()).collect();

            assert_eq!(perm.domain == test.domain, true);
            assert_eq!(perm.actions == expected_actions, true);
            assert_eq!(perm.targets == expected_targets, true);
        }
    }

    #[test]
    fn test_part_from_str() {
        let tests = vec![(Some(""), vec!["*"]),
                         (None, vec!["*"]),
                         (Some("action1, action2, action3"),
                          vec!["action1", "action2", "action3"]),
                         (Some("incorrect format"), vec!["incorrect format"])];
        for &(ref input, ref expected) in tests.iter(){
            let expected_result: HashSet<String> =
                expected.iter().map(|x| x.to_string()).collect();
            let result: HashSet<String> = Permission::part_from_str(*input);
            assert_eq!(result, expected_result);
        }
    }

    #[test]
    fn test_implies_from_str() {
        let perm = Permission::new("domain1:action1");
        assert_eq!(perm.implies_from_str("domain1:action1:target1"), true);
        assert_eq!(perm.implies_from_str("domain1:action2"), false);
    }

    #[test]
    fn test_implies_from_perm() {
        let perm1: Permission = Permission::new("domain1:action1");
        let perm2: Permission = Permission::new("domain1:action1,action2");
        let perm3: Permission = Permission::new("domain1:action1,action2:target1");
        let perm4: Permission = Permission::new("domain1:action3,action4:target2,target3");
        let perm5: Permission = Permission::new("domain1:action1,action2,action3,action4");
        let perm6: Permission = Permission::new(":action1,action2,action3,action4");
        let perm7: Permission = Permission::new("domain1:action1,action3:target1, target2");
        let perm7b: Permission = Permission::new("domain1:action5");
        let perm8: Permission = Permission::new("");

        assert_eq!(perm5.implies_from_perm(&perm1), true);
        assert_eq!(perm5.implies_from_perm(&perm2), true);
        assert_eq!(perm5.implies_from_perm(&perm3), true);
        assert_eq!(perm5.implies_from_perm(&perm4), true);
        assert_eq!(perm1.implies_from_perm(&perm5), false);
        assert_eq!(perm3.implies_from_perm(&perm5), false);
        assert_eq!(perm6.implies_from_perm(&perm7), true);
        assert_eq!(perm7.implies_from_perm(&perm6), false);
        assert_eq!(perm6.implies_from_perm(&perm7b), false);
        assert_eq!(perm7b.implies_from_perm(&perm6), false);
        assert_eq!(perm8.implies_from_perm(&perm1), true);
        assert_eq!(perm1.implies_from_perm(&perm8), false);
        assert_eq!(perm8.implies_from_perm(&perm2), true);
        assert_eq!(perm2.implies_from_perm(&perm8), false);
        assert_eq!(perm8.implies_from_perm(&perm3), true);
        assert_eq!(perm3.implies_from_perm(&perm8), false);
        assert_eq!(perm8.implies_from_perm(&perm4), true);
        assert_eq!(perm4.implies_from_perm(&perm8), false);
        assert_eq!(perm8.implies_from_perm(&perm5), true);
        assert_eq!(perm5.implies_from_perm(&perm8), false);
        assert_eq!(perm8.implies_from_perm(&perm6), true);
        assert_eq!(perm6.implies_from_perm(&perm8), false);
        assert_eq!(perm8.implies_from_perm(&perm7), true);
        assert_eq!(perm7.implies_from_perm(&perm8), false);
    }

    #[test]
    fn test_internalis_permitted_from_str(){
        let required: &str = "domain2:action4:target7";
        let assigned: Vec<&str> = vec!["domain1:action1",
                                       "domain2:action3,action4"];
        assert_eq!(is_permitted_from_str(required, assigned.into_iter()), 1);
    }

    #[test]
    fn test_is_permitted_from_perm() {
        let required: &str = "domain2:action4:target7";
        let assigned: Vec<Permission> = vec![Permission::new("domain1:action1"),
                                             Permission::new("domain2:action3,action4")];
        assert_eq!(is_permitted_from_perm(required, assigned), 1);
    }

    #[test]
    fn test_perms_from_buffer() {
        let permissions: Vec<Permission> = vec![Permission::new("domain1:action1"),
                                                Permission::new("domain2:action3,action4")];
        let serialized = serde_json::to_string(&permissions).unwrap();
        let result: Vec<Permission> =
            perms_from_buffer(serialized.as_bytes()).unwrap();
        assert_eq!(result, permissions);
    }
}
```
