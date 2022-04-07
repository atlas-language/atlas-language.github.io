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

Notice that we don't need a return statement. The last expression in a function's body gets returned.

# Filesystems

Filesystems in Atlas are first class objects. Let's see a feq examples.


## A Toy Example

```
let fs = {
    "entries": {
        "foo.txt":{
            "content": "this is the content of foo.txt"
        }
        "my_folder":{
            "entries":{
                "bar.txt": {
                    "content":"this is the content of bar.txt"
                }
            }
        }
    }
}
```

This object specifies a folder containing `foo.txt` and subdirectory called `my_folder` which contains `bar.txt`.
There isn't much we can do with this filesystem since it doesn't have any executables.

We've prewritten a filesystem [here](https://raw.githubusercontent.com/atlas-language/samples/3cefece7cb2116d8918c7748f45f99e0bc1a59be/bash.at), which contains a couple basic shell utilities like `bash`, `cat`, and `echo`.
It's easy to fetch this filesystem, even though it's not on our machine.

Notice that the entries on this filesystem are stored on the remote sample repository. Also notice that each file is
defined by a function call. Since Atlas is lazy, they won't be fetched from the server until we use them.

```
let fs = import("https://raw.githubusercontent.com/atlas-language/samples/3cefece7cb2116d8918c7748f45f99e0bc1a59be/bash.at").fs;
```

Now we can pass `fs` to the `exec` function, along with the binary we wish to run, and any arguments.

```let fs_modified = exec(fs, "/", "/bin/bash", []);```

When exec gets called, lots of special things happen under the hood. 
First, the `fs` variable gets mounted as a read-only FUSE filesystem. 
Then we make an writable overlay so tools like `touch` can write to it. 
We can then execute the binary in this overlay, effectively creating a container that is isolated from the host operating system.
All the while, all the files in `fs` hasn't even been downloaded!
We don't need to until the `bash` binary interacts with a specific file. *We get this optimization for free*
since Atlas is built from the ground up lazy.

Notice that we get a new filesystem object, `fs_modified`. This is a new filesystem object, which contains
the original `fs` files as well as any modifications that were made when `bash` was was run. We can resuse
`fs_modified` to compose other operations we might want to run.

Here is a demo of this exact workflow:

<script src="https://asciinema.org/a/1FODZaoDtGNjuaDVDVvYzJVsg.js" id="asciicast-1FODZaoDtGNjuaDVDVvYzJVsg" async></script>

## Applications

This was a toy example. Let's see how Atlas can be used in a more practical setting.
Suppose we have a project that we have written in C, and we are using some compiler specific features
from a specific version of `gcc`. In a Makefile, we have no way of specifying the precise compiler
binary that gets used in the build. Make will use whatever version happens to be located at `/usr/bin` --
not great for reproducibility.

We envision an Atlas repository for each version of the `gcc` compiler, much like there is a Dockerfile for
each version of `node`. Each repository contains a binary for the **exact** `gcc` version. Just like in our `bash`
example, a user can fetch it over the internet to compile their code exactly as it was intended.

```
let gcc = import("https://raw.githubusercontent.com/atlas-language/samples/main/gcc11.2.at");
let exe = gcc.compile(fetch("sample.c"));
```

In this example, we specify exactly which `gcc` version we want to use (11.2), and use it to compile
a basic C file. We could do the same for libraries that we might want to link against too!
