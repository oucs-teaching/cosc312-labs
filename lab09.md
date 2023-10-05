# COSC312 Lab 09—Rust and CHERI machine code

## Experimentation with Rust

Rust is a programming language increasingly being adopted for systems-level programming that provides many safety benefits compared to memory "unsafe" languages such as C and C++.

Although Rust is not a hardware technology, the powerful safety constraints that it offers at compile time are analoguous to the powerful hardware-supported safety constraints offered by memory capability CPUs, such as CHERI.

- In the manner of last week's lab, clone the Git repository at into a working directory (underneath `c:\windows\temp` on the Owheo Lab computers for now) https://altitude.otago.ac.nz/cosc412/rust-introduction
- Start Docker Desktop.
- Open your working directory within Visual Studio Code, reopening that folder as a Dev Container (after having waited for Docker Desktop to finish starting up).
- When your Dev Container finishes starting, open a terminal within VSCode and run the command: `cargo new rust_intro`
- You will see a package directory has been created, and should open the file `rust_intro/src/main.rs` for editing.
- You can test that the default Rust application works using the command `cargo run` in the direcotyr that contains the `Cargo.toml` file.

### Demonstrating Rust ownership typing

- Now change the `main()` program's code to the following:
```rust=
fn main() {
    let my_string1 = String::from("Kia ora!");
    let my_string2 = my_string1;

    println!("{}",my_string2);
}
```
- Notice that VSCode indicates the type inference on variables such as `my_string1` and `my_string2` showing `: String` after both (this is not actually saved into the source code, but doing so is valid Rust, too).

:::success
:pencil:
**Task** (recommended)
Now modify the `println!` statement to instead print `my_string1`. Your VSCode editor will immediately indicate problems: a warning that `my_string2` is unusued, but more importantly a problem regarding `my_string1`.

Ignore this (useful!) editor feedback, attempt to compile your code anyway, and notice the extraordinarily detailed (and rather helpful) feedback that the compiler gives you.
:::

### Demonstrating Rust thread safety

- Compile and run the following Rust code:
```rust=
use std::thread;
use std::sync::{Arc, Mutex};

fn main() {
    let counter = Arc::new(Mutex::new(0));
    let mut handles = vec![];

    for _ in 0..10 {
        let counter = Arc::clone(&counter);
        let handle = thread::spawn(move || {
            for _ in 0..1_000_000 {
                let mut num = counter.lock().unwrap();
                *num += 1;
            }
        });
        handles.push(handle);
    }

    for handle in handles {
        handle.join().unwrap();
    }

    println!("Result: {}", *counter.lock().unwrap());
}
```
As an overview of the above code:
- Lines 1 and 2 import Rust modules that are used: `thread` is for spawning independent threads of computation; `Arc` stands for atomic reference counting and is used to ensure that duplicated objects are freed when there are no further users of an object; and `Mutex` which is a mechanism for safe concurrency that ensures only one thread is making updates to data at a time (in this case, updating a counter variable).
- Line 5 creates the integer that we care about as our counter (`0`), protects it from concurrent access by wrapping it in a `Mutex` and then wraps that in a means to be able to safely share and automatically clean up references to that `Mutex` from different threads.
- Line 6 creates an empty vector of threads
- The loop starting at line 8 creates 10 threads that will madly increment the shared counter.
- Line 9 increases the reference count of the `counter` Mutex: when this variable goes out of scope, the reference count will be decremented automatically.
- Line 10 creates a new computing thread. Note that the `move` keyword indicates that Rust ownership typing should have the thread take ownership of variables it uses, the `counter` variable in this case.
- Line 12 inside the thread's code locks the Mutex and retrieves the integer inside it, i.e., our counter, into the mutable integer variable `num`.
- Line 13 increments `num` and would be unsafe were we not to use the Mutex.
- Line 16 notes down all the thread handles in the `handles` vector.
- The loop at line 19 uses the thread handles to ensure that we have waited for each thread to finish (`join()`). The `unwrap` that is done is (ahem) skipping error handling.
- Finally line 23 outputs the result of the counter operation.



:::success
:pencil:
**Task** (optional)
While it is nice that the threading is safe, try to determine the overhead that this threading is causing: e.g., time the above code, and then time code that achieves a similar computational effect.
:::

:::success
:pencil:
**Task** (very optional)
See if you can change the above code to operate in a way where the threads interleave with a race condition. It is likely that the code would need to get even more complex, since an unsafe threading version is likely to involve highly non-idyomatic Rust code.
:::

## Exploring CHERI machine code

Rather than building a complete CHERI emulator, we can instead just explore some of the machine code generated when supporting CPU memory capabilities using the Compiler Explorer tool.

- Navigate to https://cheri-compiler-explorer.cl.cam.ac.uk/ (note that this site has worked best for me using Google Chrome)
- You should see a display such as: ![](https://hackmd.io/_uploads/rk8PgMhl6.png)

:::success
:pencil: 
**Task** (recommended)
Compare the "Purecap CHERI-RISV64" code for the number squaring code with the "RISCV64 (without CHERI)" option.

Note that hovering over the lines and variables in the C++ source code will visually highlight different (relevant) parts of the machine code that the compiler produces.

The `mulw` in both cases is the actual multiply instruction. Note however, that the opcodes in CHERI that begin with `c` are usually CPU instructions for handling capabilities: in pure capability code, capabilites get destroyed if they are touched by non-capability CPU instructions.

The "Spill" and "Reload" comments relate to when CPU registers are being saved and restored from the stack. Note that the sizes are different between the capability and non-capability code---why?

This code very much uses stack frames as we have discussed in the code security lecture.
:::

:::success
:pencil: 
**Task** (optional)
Experiment with the other code examples and code of your own design, e.g., attempt to create a buffer overflow, of the type seen in last week's lab.
:::