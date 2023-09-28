# COSC312 Lab 08—Programming language security

The objective for this lab is for you to get hands on experience with a few of the types of code bugs that have a security effect.

## Setting up your code environment

In a similar manner to the previous lab, this lab is intended to be performed within a Linux VM using Docker and Visual Studio Code's "Dev Conainters".

- Clone the public Git repository https://altitude.otago.ac.nz/cosc412/programming-language-security-exploration/ into a working copy directory of your choice.
:::danger
:bomb: Having said the working directory is your choice, actually, if you're working on the Owheo CS Lab computers, you probably should create your directory under `c:\windows\temp\` (Powershell or `cmd.exe`) or `/c/windows/temp/` (Git Bash) to avoid permissions problems regarding Docker's ability to open your files.
:::
- Start Visual Studio Code and open your working copy directory (as a directory). I do this by opening a shell, `cd`ing into my working directory, and then running `code .` to open Visual Studio Code on that directory.
- Note the prompt at the bottom right to that includes the button "Reopen in Container".
- Docker Desktop will eventually indicate that VSCode has successfully built and started your development container.
- Eventually you should see a terminal window pane in VSCode that finishes with "Done. Press any key to close the terminal."

## Exploring local variable corruption

- In VSCode open the file `test-local-var-corruption.c` and have a quick look through it.
- Open a terminal in VSCode and ensure that it is running in your container (for me that is made clear by the shell prompt's directory path starting with `/workspaces` which does not correlate to a local file path in my normal user account).
- Compile and run the code (inside the VSCode terminal on your container). The output should be:
```
buffer1: String 1.
buffer2: String 2.
```
- Now, just above the `printf` calls, introduce a `strcpy` call that copies a short string to `buffer1`. I used `strcpy(buffer1, "Hello!");`.
- Compile and run yur file again, and you should see the string updating as you would expect.

:::success
:pencil: 
**Task** (recommended)
- Explore what happens when you instead `strcpy` the literal string "Hello, World!" into `buffer1`.
- Then note the difference if you use the variable `some_string` as the source of the string copy.
- Explain what is going on when you copy the same source string into `buffer2` instead of `buffer1`. What does this tell you about the arranagement of the memory used to store local variables?
- Finally, expand the size of the string until your program crashes, as opposed to just corrupting data.
:::

:::success
:pencil: 
**Task** (recommended)
- Within the same program, now use the safer `strncpy` function. This function takes a maximum number of bytes that it will copy.
- What happens if you copy a long source string to `buffer` using exactly the maximum size of `buffer1`? If something appears unexpected, does it change as you re-run the program?
:::

## Exploring return-oriented programming (ROP)

- In VSCode open the file `test-rop-example.c` and have a quick look through it.
- Create a zero-byte file by running `touch blank` in the terminal of your dev container.
- Compile and run `test-rop-example.c`. As you'll have seen within the source code, when a file is unable to be opened, the program exits. You can use the filename `blank` to succeed reading a file that is also safe to read.

:::success
:pencil: 
**Task** (recommended)
- Run the compiled `test-rop-example` a few times from the shell. What do you notice about the output from the command? (NB: this question does not apply usefully to Windows running through Git Bash!)
- Now run the program within the `gdb` debugger and compare what happens to the output of the commmand. To run your program using `gdb`, first execute `gdb ./test-rop-example` and after the debugger starts use the debugger command `run`.
:::

- Recompile your `test-rop-example` executable program to include debugging symbols: e.g., add the `-g` option to `gcc`.
- Within the `gdb` debugger, you can analyse and manipulate memory while your program is running. Let's ma ually apply a ROP attack.
- Load `test-rop-example` into the `gdb` debugger in the same manner as above.
- Use `break vulnerableFunction` (note that when it can, `gdb` has rich support for tab completion) to set a breakpoint at the start of that function.
- In GDB `run` your program and give it the name of your zero byte file. This should hit the breakpoint you set above, and return you to the GDB prompt.
- Issue the `step` command to run the assignment to the integer `marker` (which is just to give you a visible marker in memory).
- Run the command `x/20xg $sp`. This will display 20 64-bit hexadecimal numbers starting at the current value of the CPU's stack pointer.
- Within the hexadecimal output, you should be able to spot the marker variable's memory.
- Now let's manually corrupt the stack by overwriting the return address. The location of the return address is 24 bytes after the end of the `buffer` variable. The location of the `secretFunction` is in the earlier output of the program, but GDB also knows it by name. So an invocation such as `set {long long}(buffer+64+24) = secretFunction` should get the job done.
- You can repeat the `x/20xg $sp` command to check that memory changed as expected.
- The `continue` command will resume the execution of your program. Unfortunately when trying to work around the broken Docker installation on the Owheo labs in Windows, this effect does not work as it should. On Linux systems you should see that the code executes `secretFunction`, and then crashes, as the stack has been corrupted.

:::success
:pencil: 
**Task** (optional challenge)
Create a file that you can load into `test-rop-example` that causes the program, when freshly loaded into the debugger, to run `secretFunction`.
:::