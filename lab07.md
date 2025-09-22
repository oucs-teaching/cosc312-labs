---
title: COSC312 Lab week 10—Homomorphic encryption
tags: [cosc312, lab]

---

# COSC312 Lab week 10—Homomorphic encryption

- The objective for this lab is for you to experiment with the open source SEAL library from Microsoft, that implements homomorphic encryption.

:::info
:hourglass_flowing_sand: 
It's a busy time of semester, so this lab exercise is intended to involve less time and effort than some past lab exercises to reach instructive evaluation results and programming exercises.
:::

## Setting up your software environment

- Start the Docker application (i.e., Docker Desktop)
- Clone the public Git repository https://altitude.otago.ac.nz/cosc412/homomorphic-encryption-using-seal into a working copy directory of your choice.
:::danger
:bomb: Having said the working directory is your choice, actually, if you're working on the Owheo Building Lab computers, you probably should create your directory under a local directory (as you have been doing in the past). One place that (hopefully) works is `c:\windows\temp\` (PowerShell or `cmd.exe`) or `/c/windows/temp/` (Git Bash) to avoid permissions problems regarding Docker's ability to open your files.
:::
- Start Visual Studio Code and open your working copy directory (as a directory). Because I have the Visual Studio Code command line tools instealled, I can do this by opening a shell, `cd`ing into my working directory, and then running `code .` to open Visual Studio Code on that directory (this should work on the Owheo Lab computers too). I see something akin to: ![](https://hackmd.io/_uploads/H1EsiFYkT.png)
- You should be prompted in the bottom right of the VSCode with the choice to "Reopen in Container", and you should click that button.
- If you consult your Docker Desktop application after a while you should see something like: ![](https://hackmd.io/_uploads/HJNDntFy6.png) which indicates that VSCode has successfully started your development container.
- Eventually you should see a terminal window pane in VSCode that finishes with "Done. Press any key to close the terminal."

## Testing PySEAL

- Within VSCode open the file `homomorphic-test-1.py` in an editor window.
- On VSCode's left button pannel focus the "Run and Debug" section and click "Run and Debug". Choose "Python file" from the drop-down list that appears. (Or you can just run `python3 homomorphic-test-1.py` from the terminal pane of VSCode.)
- You should see a terminal pane in which the Python script is run, and you should see the results of an addition and a multiplication result.

## FYI: Building PySEAL

- This is not part of the lab exercise in that the Git repository distributes a pre-built instance of the SEAL library (i.e., that's what the `seal.cpython-311-x86_64-linux-gnu.so` file is).
- If you do want to rebuild the library for yourself, e.g., to run on a different Python version and/or a different operating system, the `README.md` file links out to the instructions on the repository from which PySEAL was downloaded, and by which the compiled library included in the Git repository was built.

## Exercises

:::success
:pencil: 
**Task** (recommended) Examine the flow of the Python code, and read the code comments.
:::

:::success
:pencil: 
**Task** (recommended) Change the size of the `scale` factor. Observe how the noise changes for different values of `scale`.
:::

:::success
:pencil: 
**Task** (recommended) Change the code to perform operations in a loop with a selectable iteration count. Show if/how errors accumulate for repeated operations.
:::

:::success
:pencil: 
**Task** (optional) Benchmark the speed of homomorphic encryption compared to equivalent native Python arithmetic. This is likely best done by timing a high iteration count of a loop that runs the same operations directly or using homomorphic encryption. Keep in mind that this is a very rough performance comparison, though, since you are performing the computation within a Docker container that by default does not have full access to all of the CPU cores on your computer.
:::

## Cleaning up

- Run the Visual Studio Code command to reopen the file locally (from the command pallete, "Dev Containers: Reopen Folder Locally").
- In Docker Desktop's GUI or otherwise, delete the container and any (Docker) images associated with that container. 
- Quit Docker Desktop and ensure that any menubar (macOS) or system tray (Windows) items have gone. (Otherwise these Docker items can be used to access a menu from which to request Docker to shut down.)# COSC312 Lab week 10—Homomorphic encryption

- The objective for this lab is for you to experiment with the open source SEAL library from Microsoft, that implements homomorphic encryption.

:::info
:hourglass_flowing_sand: 
It's a busy time of semester, so this lab exercise is intended to involve less time and effort than some past lab exercises to reach instructive evaluation results and programming exercises.
:::

## Setting up your software environment

- Start the Docker application (i.e., Docker Desktop)
- Clone the public Git repository https://altitude.otago.ac.nz/cosc412/homomorphic-encryption-using-seal into a working copy directory of your choice.
:::danger
:bomb: Having said the working directory is your choice, actually, if you're working on the Owheo Building Lab computers, you probably should create your directory under a local directory (as you have been doing in the past). One place that (hopefully) works is `c:\windows\temp\` (PowerShell or `cmd.exe`) or `/c/windows/temp/` (Git Bash) to avoid permissions problems regarding Docker's ability to open your files.
:::
- Start Visual Studio Code and open your working copy directory (as a directory). Because I have the Visual Studio Code command line tools instealled, I can do this by opening a shell, `cd`ing into my working directory, and then running `code .` to open Visual Studio Code on that directory (this should work on the Owheo Lab computers too). I see something akin to: ![](https://hackmd.io/_uploads/H1EsiFYkT.png)
- You should be prompted in the bottom right of the VSCode with the choice to "Reopen in Container", and you should click that button.
- If you consult your Docker Desktop application after a while you should see something like: ![](https://hackmd.io/_uploads/HJNDntFy6.png) which indicates that VSCode has successfully started your development container.
- Eventually you should see a terminal window pane in VSCode that finishes with "Done. Press any key to close the terminal."

## Testing PySEAL

- Within VSCode open the file `homomorphic-test-1.py` in an editor window.
- On VSCode's left button pannel focus the "Run and Debug" section and click "Run and Debug". Choose "Python file" from the drop-down list that appears. (Or you can just run `python3 homomorphic-test-1.py` from the terminal pane of VSCode.)
- You should see a terminal pane in which the Python script is run, and you should see the results of an addition and a multiplication result.

## FYI: Building PySEAL

- This is not part of the lab exercise in that the Git repository distributes a pre-built instance of the SEAL library (i.e., that's what the `seal.cpython-311-x86_64-linux-gnu.so` file is).
- If you do want to rebuild the library for yourself, e.g., to run on a different Python version and/or a different operating system, the `README.md` file links out to the instructions on the repository from which PySEAL was downloaded, and by which the compiled library included in the Git repository was built.

## Exercises

:::success
:pencil: 
**Task** (recommended) Examine the flow of the Python code, and read the code comments.
:::

:::success
:pencil: 
**Task** (recommended) Change the size of the `scale` factor. Observe how the noise changes for different values of `scale`.
:::

:::success
:pencil: 
**Task** (recommended) Change the code to perform operations in a loop with a selectable iteration count. Show if/how errors accumulate for repeated operations.
:::

:::success
:pencil: 
**Task** (optional) Benchmark the speed of homomorphic encryption compared to equivalent native Python arithmetic. This is likely best done by timing a high iteration count of a loop that runs the same operations directly or using homomorphic encryption. Keep in mind that this is a very rough performance comparison, though, since you are performing the computation within a Docker container that by default does not have full access to all of the CPU cores on your computer.
:::

## Cleaning up

- Run the Visual Studio Code command to reopen the file locally (from the command pallete, "Dev Containers: Reopen Folder Locally").
- In Docker Desktop's GUI or otherwise, delete the container and any (Docker) images associated with that container. 
- Quit Docker Desktop and ensure that any menubar (macOS) or system tray (Windows) items have gone. (Otherwise these Docker items can be used to access a menu from which to request Docker to shut down.)