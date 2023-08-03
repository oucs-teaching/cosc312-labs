## Lab objective: reproducing Keberos VM functionality

A brief demonstration was given of Kerberos effecting access to an SSH server during lectures. The lecture note PDF indicates how to run the COSC412 (and COSC312) demonstration virtual machine (VM) to access a standardised software environment.

One objective of the lab this week is to ensure that everyone in the class is comfortable with the use of the VM tools used to reproduce demonstrations discussed in lectures. Depending on which papers you have completed, you may already be entirely comfortoable with these tools.

## Working on your own computer

I'm keen to support use of the COSC312 VM on your own computers as well as the computers in the CS Labs. The COSC312 VMs should be usable on Windows, macOS (Intel CPUs) and Linux. You would need to install VirtualBox and Vagrant, both of which are available across the aforementioned operating systems.

A challenge at the moment is that Apple computers with Arm CPUs (e.g., M1, M2, etc.): these computers do not presently have production support from VirtualBox, and thus Vagrant cannot work either. If there is sufficient demand I will sort out a work-around for this.

## Working in the CS Labs

When working in the CS Lab environment, on Windows I typically use the Git Bash shell, since I more often use Unixy shells than Windows ones. That said, I would be keen to include instructions for PowerShell and `cmd.exe` where it is straightforward to do so.

:::warning
:bomb: Something seems to be wrong with the CS Lab computers when they first start VMs. However this work-around should hopefully unstick whatever the problem is until the sysadmins fix it properly.

When you first try to run `vagrant up` the VM that Vagrant starts in VirtualBox seems to get jammed during startup. I recommend that you:
- start the VirtualBox application
- select the VM that is "Running" and click the "Show" toolbar button. This seems to unstick the VM and get things moving again.
- return to your terminal window and run the command `vagrant reload`, which will cause Vagrant to notice that it can't SSH to the VM, force it to shut off, and then restart it and configure it properly the second time.
:::

In the CS Lab environment I have reported some general issues with network speed, and know that the sysadmins are working to improve how the CS Lab environment works in terms of your user experience (particularly when moving between different physical lab computers).

## Suggested lab exercises

:::info
:bulb: COSC312 labs are not assessed, so there is no marking-based incentive to experiment with Kerberos. The tasks below have beside them:
- "recommended" for cases that are likely to strengthen your overall understanding of Keberos theory and fundamentals; and
- "optional" for tasks that are suggestions of further exploration you can undertake.
:::

:::success
:pencil: 
**Task One** (recommended) Confirm that you can produce the same results as presented in the lecture notes on  the CS Lab computer in front of you and/or on your own laptop.
:::

Now that you are sure that you can reproduce the steps demonstrated in the lecture, you should link the demonstration steps with the Kerberos theory.

:::success
:pencil: 
**Task Two** (recommended) In the SSH server accessing example for task one, figure out which Kerberios commands correspond to when different messages are sent, as shown in the lecture notes.
:::

The idea of renewable tickets was discussed in the lecture. The aim of the third task is to experiment with the behaviour of renewable tickets.

:::success
:pencil: 
**Task Three** (optional) Reconfigure the ticket lifetimes to allow them to be renewable, but to have very short (but usable) ticket expiry. Explore the example of accessing the SSH server in the above demo, either allowing your service ticket to expire or not. Do you detect differences in behaviour from Kerberos? What can you determine from (repeatedly) running the `klist` command?
:::

Cross-realm authentication was briefly discusssed in lectures: see whether you are able to configure and demonstrate cross-realm authentication working.

:::success
:pencil: 
**Task Four** (extension) Set up two Kerberos realms, and step through the process of cross-realm authentication. Your configuration should allow one realm (`A`) to acquire a ticket granting ticket for the other realm (`B`), and to connect to the SSH server that accepts credentials in the second realm (`B`). Can you then extend this one-way trust into two-way trust and demonstrate that Kerberos is working as expected?
:::
