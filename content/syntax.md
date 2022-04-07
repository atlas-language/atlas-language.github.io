+++
title = "Syntax"
description = "Syntax"
+++

Atlas is a functional language with syntax resembling Python and Rust.

# Variables

Varible declarations in Atlas are marked by the `let` keyword and ended with a semicolon.
Variables in Atlas are immutable.
Lets assign a variable `x` the value `1`:

```
let x = 1;
```

If we want this variable to get exposed when another Atlas file imports it, we add a pub keyword:

```
pub let not_secret = "This can be accessed by other files!";
```


# Datatypes

Atlas has all the primitive data types you would expect (integers, floats, bools, strings).
Atlas also features lists, records, and tuples. Here's an example:
```
    let person = {
        "name": "Plato",
        "alive": (428, 348),
        "dead": true,
        "books": ["Apology", "Republic", "Symposium"]
    };

    let platosName = person["name"];
```

# Functions

We can define functions in Atlas like this:

```
def doSomeMath(x) {
    let incr = x + 1;
    incr * 2
}
```

```python
c = [1,2,3]
```

Notice that we don't need a return statement. The last expression in a function's body gets returned.