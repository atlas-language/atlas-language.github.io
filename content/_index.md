+++
title = "Atlas"
description = "Atlas Overview"
+++

# Installation

You can download a pre-alpha binary 
[here](https://github.com/atlas-language/atlas/releases/download/demo-prealpha/atlas-x86\_64.AppImage).
Note that you need a very recent linux kernel version (> 5.11) in order to run the application as we use recent features for unprivileged containers.

# Syntax

Atlas is a functional language with syntax resembling Python and Rust.

## Variables

Varible declarations in Atlas are marked by the `let` keyword and ended with a semicolon.
Variables in Atlas are immutable.
Lets assign a variable `x` the value `1`:

```rust
let x = 1;
```

If we want this variable to get exposed when another Atlas file imports it, we add a pub keyword:

```rust
pub let not_secret = "This can be accessed by other files!";
```


## Datatypes

Atlas has all the primitive data types you would expect (integers, floats, bools, strings).
Atlas also features lists, records, and tuples. Here's an example:
```rust
    let person = {
        "name": "Plato",
        "alive": (428, 348),
        "dead": true,
        "books": ["Apology", "Republic", "Symposium"]
    };

    let platosName = person.name
```

## Functions

We can define functions in Atlas like this:

```rust
fn do_math(x) {
    let incr = x + 1;
    let mul = incr * 2;
    mul / incr
}
let a = do_math(3)
```

Notice that we don't need a return statement. The last expression in a function's body gets returned.