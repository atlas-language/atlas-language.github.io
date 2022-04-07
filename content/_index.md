+++
title = "Atlas"
description = "Atlas Overview"
+++

# Installation

Installation instructions coming soon! Note that to use the userspace containers, you need a relatively modern kernel (>= 5.11)

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

```rust
c = [1,2,3]
```

Notice that we don't need a return statement. The last expression in a function's body gets returned.