+++
title = "Filesystems"
+++

### A Toy Example

Filesystems in Atlas are first class objects. Let's handwrite one here:

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

### Applications

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
