+++
date = "2016-11-21T15:03:12-05:00"
next = "/01.project_crate"
title = "Rust Tutorial:  authz"
toc = true
weight = 0
+++

## <center>Welcome to the Tutorial!</center>
<br>

The goal of this tutorial is to teach Rust-specific concepts using a real-world application of the language.

This tutorial examines the **authz** crate, available at [crates.io](https://crates.io/crates/authz) and [github](https://www.github.com/Dowwie/rust-authz).

You are presented a library that enables permission-based authorization.  The business logic used to faciliate permission-based authorization is based on an implementation written in Python for the [Yosai security framework](https://yosaiproject.github.io/yosai). This is not a tutorial about authorization but a brief explanation of what it entails is provided below.


### <center> Permission-based Authorization, in a Nutshell </center>

This project teaches Rust concepts using Permission-based authorization as its case study. A Permission states what behavior can be performed in an application but not who can perform it. Permissions are modeled such that a developer may choose an appropriate level of detail (granularity) that suits the authorization policy governing a software application. For more information about permission-based authorization, read the [Yosai documentation about authorization](https://yosaiproject.github.io/yosai/authorization).

Permission-based authorization is made possible through interaction with a Permission data type.  A Permission type is evaluated and compared with another Permission type, determining whether one implies the other.  Authorization is granted when the Permission(s) assigned to a user imply the Permission(s) that are required to perform a behavior in a system.  Consequently, the Permission struct and its implementation are the subject of this project.
