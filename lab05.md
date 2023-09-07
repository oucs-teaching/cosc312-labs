# COSC312 Lab 05—Ethereum blockchain

The objective for this lab is:
- for you to experiment with the Ethereum blockchain operating on a safe, isolated and private network.

## Setting up your Ethereum environment

:::danger
:bomb: The Windows environment in the Owheo CS Labs breaks Docker's ability to share files and folders between containers (lightweight virtual machines) and parts of the host system, such as your home directory. You can work around the problem in this lab by doing the exercises within the `c:\Windows\temp` directory instead of your home directory.

This lab does not require you to keep any files for the future, but do remember that if you want to keep any files, you will need to copy them back from the aforementioned Windows temporary directory to a location such as your home directory.
:::

:::warning
:warning: Starting up Docker Desktop on the Owheo CS Lab computers is a bit of a strange experience. For the steps that worked for me, have a look at just the [Using the Docker Desktop application](https://hackmd.io/BrkRNRX5RomHMSsvq82Aaw#Using-the-Docker-Desktop-application) section in the COSC349 labs. (I'm linking to that rather than copying the material here so that if I need to change the description, I don't end up with inconsistent instructions on different lab exercise pages.,)
:::

These instructions assume that you have Docker up and running. If you are using Mac or Windows, it is recommended that you explore the Docker Desktop GUI as it is a convenient way to see the activity logs of your Ethereum nodes, and to control all of the Docker resources being used.

Clone the code repository https://altitude.otago.ac.nz/cosc412/ethereum-testing into a directory of your choice and change into that directory. This repository provides the `docker-compose.yml` file that describes to the Docker Compose tool how to set up the three Ethereum nodes, interconnect them wtih a private network, and connect some of their file storage so that it is shared between the containers and the host computer.

Briefly examine the `genesis.json` file. It provides initial configuration of our blockchain.

Initialise the three Ethereum nodes using:
```
docker-compose run --entrypoint "geth --datadir /eth-data init /genesis.json" eth-node-1
docker-compose run --entrypoint "geth --datadir /eth-data init /genesis.json" eth-node-2
docker-compose run --entrypoint "geth --datadir /eth-data init /genesis.json" eth-node-3
```

You can examine the files that have been created in the shared volumes: the `/eth-data` folder inside each of the three computational containers is mapped to `eth-node-1`, `eth-node-2` and `eth-node-3` on your host computer.

After doing so, for me `eth-node-1` remains running, but the other containers exit as soon as they have configured the files in their respective `/eth-data` folders.

Let's create two wallet accounts that are managed by `eth-node-1`, and then set those accounts to be the target of mining operations.

```
docker-compose run --entrypoint "geth --datadir /eth-data account new" eth-node-1
```

You will need to enter a password when prompted. Note the public key "address" that is shown. For me this was `0xBf1B07d6F1759fA380F07BCd015D04e306731640` for the first account and `0x998BD36254bBB2345d3aD5Fe1527804B9bEDC6AD` for the second.

Edit the `docker-compose.yml` file's `eth-node-1` section. Delete the line above the `--mine` directive, uncomment the `--mine` and `--miner` lines, and paste the first public key address as the `etherbase`. This public key address will get "paid" the mining rewards.

Now let's shut down `eth-node-1` and start it again, using:
```
docker-compose down eth-node-1
docker-compose up eth-node-1 -d
```

Use the Docker Desktop GUI to watch the logs for your running `eth-node-1`. You should see lines stating "Generating DAG in progress". This is the precursor to the proof-of-work occurring. After about four minutes, you should see messages such as "mined potential block" and "block reached canonical chain".

Let's check the account balance of all accounts managed by `eth-node-1`. Connect to that node's `geth` console using:

```
docker-compose exec -it eth-node-1 geth attach ipc://eth-data/geth.ipc
```

You should be greated with a `>` prompt, which is expecting JavaScript. Type `exit` when you want to exit.

To list the balance of all accounts managed by this node, converted to ether units for readability, paste in the function:

```
eth.accounts.forEach(function(account) {
  console.log(account + ": " + web3.fromWei(eth.getBalance(account), "ether") + " ETH");
});
```

While typing up these instructions, my first account has been accumulating many mining rewards. My second account is still at balance zero.

Now, modify your `docker-compose.yml` to change your `eth-node-2` section to uncomment the mining options as for `eth-node-1`, but use the second public key as the target for mining rewards.

Start up both `eth-node-2` and `eth-node-3` by invoking `docker-compose up -d`

Using the Docker Desktop GUI, examine the logs of `eth-node-2` and `eth-node-3`.

Node three should not actually be doing anything. It is not yet linked into the ethereum network. Node two will be starting to do its own mining, but is not linked into the network either.

Find the enode of the first node by using:
```
docker-compose exec -it eth-node-1 geth --exec "admin.nodeInfo.enode" attach ipc://eth-data/geth.ipc
```

You should be shown a result such as:
```
"enode://8710195eb5c1696e0d76931ef28c8dabf9bda27598e08b270edbf2b6e9fa1a2107bff0a21f5c08674e83edd8ad41a434d3b306fd86004a02ec1df7cafb5c32c0@127.0.0.1:30303?discport=0"
```

You need to replace the `127.0.0.1` part with the IP address of `eth-node-1`, which can be found by running `ip addr` in the "Terminal" tab that Docker Desktop provides for your eth-node-1 and looking for the address that starts `172.19.0`. It is very likely that the replacement is `172.19.0.2`.

You can now communicate that to your other nodes, but you will need to change the address to be correct for your context:

```
docker-compose exec -it eth-node-2 geth --exec "admin.addPeer('enode://8710195eb5c1696e0d76931ef28c8dabf9bda27598e08b270edbf2b6e9fa1a2107bff0a21f5c08674e83edd8ad41a434d3b306fd86004a02ec1df7cafb5c32c0@172.19.0.2:30303?discport=0')" attach ipc://eth-data/geth.ipc
docker-compose exec -it eth-node-3 geth --exec "admin.addPeer('enode://8710195eb5c1696e0d76931ef28c8dabf9bda27598e08b270edbf2b6e9fa1a2107bff0a21f5c08674e83edd8ad41a434d3b306fd86004a02ec1df7cafb5c32c0@172.19.0.2:30303?discport=0')" attach ipc://eth-data/geth.ipc
```

See what happens in the logs. In my case `eth-node-2` noted (complete with scream emoji) that a number of blocks had been lost. That's because the node became aware of `eth-node-1`'s far longer fork, and switched over to it.

Meanwhile node three will be seen to be importing lots of blockchain data, but isn't set to do mining, so won't be showing any mining activity of its own.

You can check whether the added peer nodes have taken effect by asking a node what its `peerCount` is, with a command such as:

```
docker-compose exec -it eth-node-1 geth --exec "net.peerCount" attach ipc://eth-data/geth.ipc
```

## Exercises

:::success
:pencil: 
**Task** (recommended) Repeat the balance checking JavaScript invocations and convince yourself that, on average, now both accounts are gaining mining rewards at about the same rate.
:::

:::success
:pencil: 
**Task** (recommended) Add mining functionality into `eth-node-3` and reuse one of the two public keys you've already generated. You should confirm that the mining rewards, as expected, now look to be increasisng at twice the rate of the other public key.
:::

:::success
:pencil: 
**Task** (optional) Investigate the type and content of the files contained within the container's `/eth-data` folders.
:::

## Cleaning up

:::warning
:warning: Remember that the `/eth-data` created by running your ethereum test will remain even after the containers are shut down. So after doing `docker-compose down` to shut down the containers, you additionally want to `rm` the directories `eth-node-1`, `eth-node-2` and `eth-node-3`
:::
