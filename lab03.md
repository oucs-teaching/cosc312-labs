# COSC312 Lab 03—Block ciphers and HTTPS

The objective for this lab is:
- to investigate using block ciphers, experimenting with the examples shown in the COSC312 lecture on Monday.
- to explore the content and use of X.509 certificates within TLS/SSL, and specifically within the secure web protocols (HTTPS).

:::info
:eyes: For those of you whose computers are Apple Macs with Arm CPUs, I've now made an experimental (i.e., almost certainly incomplete) branch of the COSC312/COSC412 demonstration VM repository where Vagrant uses Docker Desktop to create a (lightweight) VM, instead of using VirtualBox to create a full VM.

To use this approach on your own computer you need to:
- install Vagrant (but not VirtualBox)
- install Docker Desktop
- After cloning the Git repository for the demonstration VM, run `git checkout docker` to change to the Docker-based branch
- Start Docker Desktop, and wait for it to start up (its GUI and/or its systray / dock icon should make it clear whether it is still starting up or not).
- Instead of the command `vagrant up` run the command `vagrant up --provider=docker`, after which point you should hopefully be able to rejoin the examples shown in lecture notes.
:::

## Suggested exercises exploring block ciphers

:::success
:pencil: 
**Task One** (recommended) Confirm that you can produce equivalent results to those presented in the lecture notes on using block ciphers to perform encryption and decryption, but using data that you generate.
:::

:::success
:pencil: 
**Task Two** (recommended) Experiment with explicitly corrupting ciphertext and then decrypting it and examining the resulting plaintext, e.g., by following the examples presented in the lecture notes.
:::

:::success
:pencil: 
**Task Three** (optional) See if you can perform equivalent block cipher operations in a programming language that you are comfortable with, and that provides a rich library, such as an API to OpenSSL.
:::

## Suggested exercises exploring TLS/SSL HTTPS

:::success
:pencil: 
**Task Four** (recommended) As in the lectures, set up your VM to run a web server, and confirm that you can connect and authenticate to your VM from a web browser running on your host computer.
:::

:::success
:pencil: 
**Task Five** (recommended) From a web browser running on your host computer, connect to the HTTPS server running in your VM. Access the web server's certificate from both your web browser, and using the command line (or otherwise) on your VM. Compare the different views presented of the underlying certificate data, and ensure that they match, where expected to.
:::

:::success
:pencil: 
**Task Six** (optional) Reproduce the HTTP Digest Authentication workflow included in the lecture notes, checking that it matches the actual web server behaviour.
:::
