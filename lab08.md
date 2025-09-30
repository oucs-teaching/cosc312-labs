---
title: COSC312 Lab week 11—Programming language security
tags: [cosc312, lab]

---

# COSC312 Lab week 11—Programming language security

The objective for this lab is for you to get hands on experience with a few of the types of code bugs that have a security effect.

## Setting up your code environment

In a similar manner to the previous lab, this lab is intended to be performed within a Linux VM using Docker and Visual Studio Code's "Dev Conainters".

- Clone the public Git repository https://altitude.otago.ac.nz/cosc412/programming-language-security-exploration/ into a working copy directory of your choice.
:::danger
:bomb: Having said the working directory is your choice, actually, if you're working on the Owheo Building Lab computers, you probably should create your directory under `c:\windows\temp\` (Powershell or `cmd.exe`) or `/c/windows/temp/` (Git Bash) to avoid permissions problems regarding Docker's ability to open your files.
:::
- Start Visual Studio Code and open your working copy directory (as a directory). I do this by opening a shell, `cd`ing into my working directory, and then running `code .` to open Visual Studio Code on that directory.
- Note the prompt at the bottom right to that includes the button "Reopen in Container".
- Docker Desktop will eventually indicate that VSCode has successfully built and started your development container.
- Eventually you should see a terminal window pane in VSCode that finishes with "Done. Press any key to close the terminal." (On my M-series CPU Mac this message didn't appear.)

## Exploring local variable corruption

- In VSCode open the file `test-local-var-corruption.c` and have a quick look through it.
- Open a terminal in VSCode and ensure that it is running in your container (for me that is made clear by the shell prompt's directory path starting with `/workspaces` which does not correlate to a local file path in my normal user account).
- Compile and run the code (inside the VSCode terminal on your container). The output should be:
```
buffer1: String 1.
buffer2: String 2.
```
- Now, just above the `printf` calls, introduce a `strcpy` call that copies a short string to `buffer1`. I used `strcpy(buffer1, "Hello!");`.
- Compile and run your file again, and you should see the string updating as you would expect.

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
- Within the `gdb` debugger, you can analyse and manipulate memory while your program is running. Let's manually apply a ROP attack.
- Load `test-rop-example` into the `gdb` debugger in the same manner as above.
- Use `break vulnerableFunction` (note that when it can, `gdb` has rich support for tab completion) to set a breakpoint at the start of that function.
- In GDB `run` your program and give it the name of your zero byte file. This should hit the breakpoint you set above, and return you to the GDB prompt.
- Issue the `step` command to run the assignment to the integer `marker` (which is just to give you a visible marker in memory).
- Run the command `x/20xg $sp`. This will display 20 64-bit hexadecimal numbers starting at the current value of the CPU's stack pointer.
- Within the hexadecimal output, you should be able to spot the marker variable's memory. (Just as a reality check that you are actually looking at the content of the memory supporting the stack.)
- Let's manually corrupt the return address, trying to get return from `vulnerableFunction` to jump into `secretFunction` (which is not otherwise executed).
- The program should have already output the address of the main function: the return address from the `vulnerableFunction` will a higher but nearby address.
- You can check whether the debugger can determine the location in your source code of a particular address, e.g., I once ran the following, to find the value of the `file` parameter (since that will be on the stack somewhere), and to check whether my guess that `0x0000aaaaaaaa0b5c` might have been the return address (your addresses will almost certainly be different). In this case, the debugger indicated that the address I'd asked about appears to be the start of Line 57 of "test-rop-example.c".
```
(gdb) print file
$1 = (FILE *) 0xaaaaaaab3ac0
(gdb) info line *0x0000aaaaaaaa0b5c
Line 57 of "test-rop-example.c" starts at address 0xaaaaaaaa0b5c <main+152> and ends at 0xaaaaaaaa0b68 <main+164>.
(gdb) set {long long}($sp+8) = secretFunction
(gdb) x/20xg $sp
```
- Now let's manually corrupt the stack by overwriting the return address. This may be in quite different places depending on whether you are running on Windows, macOS, or Linux, and may additionally depend on whether you are using Arm or Intel Apple hardware. (On Arm the return address may not end up on the stack at all if the function doesn't call other functions, however in our case the `vulnerableFunction` does the file I/O, so we're probably OK.)
- On some platform I tried, the location of the return address was 24 bytes after the end of the `buffer` variable. The location of the `secretFunction` is in the earlier output of the program, but GDB also knows it by name. So an invocation such as `set {long long}(buffer+64+24) = secretFunction` would corrupt the address as desired.
- On Arm macOS the return address was 8 bytes after the stack pointer, so `set {long long}(buffer+64+24) = secretFunction` achieved the desired corruption.
- You can repeat the `x/20xg $sp` command to check that memory changed as expected.
- The `continue` command will resume the execution of your program. You should see output from the `secretFunction` if you have carried out your corruption correctly. (Although the program may then subsequently crash, as the stack has been corrupted.)

:::success
:pencil: 
**Task** (optional challenge)
Create a file and make any code changes needed so that when you load your file into `test-rop-example`, it causes the program to run `secretFunction`.
:::# COSC312 Lab week 11—Programming language security

The objective for this lab is for you to get hands on experience with a few of the types of code bugs that have a security effect.

## Setting up your code environment

In a similar manner to the previous lab, this lab is intended to be performed within a Linux VM using Docker and Visual Studio Code's "Dev Conainters".

- Clone the public Git repository https://altitude.otago.ac.nz/cosc412/programming-language-security-exploration/ into a working copy directory of your choice.
:::danger
:bomb: Having said the working directory is your choice, actually, if you're working on the Owheo Building Lab computers, you probably should create your directory under `c:\windows\temp\` (Powershell or `cmd.exe`) or `/c/windows/temp/` (Git Bash) to avoid permissions problems regarding Docker's ability to open your files.
:::
- Start Visual Studio Code and open your working copy directory (as a directory). I do this by opening a shell, `cd`ing into my working directory, and then running `code .` to open Visual Studio Code on that directory.
- Note the prompt at the bottom right to that includes the button "Reopen in Container".
- Docker Desktop will eventually indicate that VSCode has successfully built and started your development container.
- Eventually you should see a terminal window pane in VSCode that finishes with "Done. Press any key to close the terminal." (On my M-series CPU Mac this message didn't appear.)

## Exploring local variable corruption

- In VSCode open the file `test-local-var-corruption.c` and have a quick look through it.
- Open a terminal in VSCode and ensure that it is running in your container (for me that is made clear by the shell prompt's directory path starting with `/workspaces` which does not correlate to a local file path in my normal user account).
- Compile and run the code (inside the VSCode terminal on your container). The output should be:
```
buffer1: String 1.
buffer2: String 2.
```
- Now, just above the `printf` calls, introduce a `strcpy` call that copies a short string to `buffer1`. I used `strcpy(buffer1, "Hello!");`.
- Compile and run your file again, and you should see the string updating as you would expect.

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
- Within the `gdb` debugger, you can analyse and manipulate memory while your program is running. Let's manually apply a ROP attack.
- Load `test-rop-example` into the `gdb` debugger in the same manner as above.
- Use `break vulnerableFunction` (note that when it can, `gdb` has rich support for tab completion) to set a breakpoint at the start of that function.
- In GDB `run` your program and give it the name of your zero byte file. This should hit the breakpoint you set above, and return you to the GDB prompt.
- Issue the `step` command to run the assignment to the integer `marker` (which is just to give you a visible marker in memory).
- Run the command `x/20xg $sp`. This will display 20 64-bit hexadecimal numbers starting at the current value of the CPU's stack pointer.
- Within the hexadecimal output, you should be able to spot the marker variable's memory. (Just as a reality check that you are actually looking at the content of the memory supporting the stack.)
- Let's manually corrupt the return address, trying to get return from `vulnerableFunction` to jump into `secretFunction` (which is not otherwise executed).
- The program should have already output the address of the main function: the return address from the `vulnerableFunction` will a higher but nearby address.
- You can check whether the debugger can determine the location in your source code of a particular address, e.g., I once ran the following, to find the value of the `file` parameter (since that will be on the stack somewhere), and to check whether my guess that `0x0000aaaaaaaa0b5c` might have been the return address (your addresses will almost certainly be different). In this case, the debugger indicated that the address I'd asked about appears to be the start of Line 57 of "test-rop-example.c".
```
(gdb) print file
$1 = (FILE *) 0xaaaaaaab3ac0
(gdb) info line *0x0000aaaaaaaa0b5c
Line 57 of "test-rop-example.c" starts at address 0xaaaaaaaa0b5c <main+152> and ends at 0xaaaaaaaa0b68 <main+164>.
(gdb) set {long long}($sp+8) = secretFunction
(gdb) x/20xg $sp
```
- Now let's manually corrupt the stack by overwriting the return address. This may be in quite different places depending on whether you are running on Windows, macOS, or Linux, and may additionally depend on whether you are using Arm or Intel Apple hardware. (On Arm the return address may not end up on the stack at all if the function doesn't call other functions, however in our case the `vulnerableFunction` does the file I/O, so we're probably OK.)
- On some platform I tried, the location of the return address was 24 bytes after the end of the `buffer` variable. The location of the `secretFunction` is in the earlier output of the program, but GDB also knows it by name. So an invocation such as `set {long long}(buffer+64+24) = secretFunction` would corrupt the address as desired.
- On Arm macOS the return address was 8 bytes after the stack pointer, so `set {long long}(buffer+64+24) = secretFunction` achieved the desired corruption.
- You can repeat the `x/20xg $sp` command to check that memory changed as expected.
- The `continue` command will resume the execution of your program. You should see output from the `secretFunction` if you have carried out your corruption correctly. (Although the program may then subsequently crash, as the stack has been corrupted.)

:::success
:pencil: 
**Task** (optional challenge)
Create a file and make any code changes needed so that when you load your file into `test-rop-example`, it causes the program to run `secretFunction`.
:::