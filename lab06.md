# COSC312 Lab week 9—More Ethereum blockchain

Since the lecture this week continued on the theme of blockchain and Ethereum, this week's lab does so too.

The objective for this lab is:
- for you to continue experimenting with the Ethereum blockchain operating on a safe, isolated and private network.

:::info
:bulb: **Ethereum 2.0 and proof of stake consensus**

It would be good to be able to explore the proof of stake system that Ethereum uses, however this is not entirely straightforward, since the scheme is bootstrapped by a proof of work phase. Since the Ethereum "merge" has happened, that brought Ethereum to proof of stake, many of the software repositories previously investigating the Ethereum proof of stake software testing have bit-rotted into oblivion, and cannot easily be brought back for your enjoyment.
:::

## Setting up your Ethereum environment

Follow the directions (and warnings) from the previous lab up to the point of the exercises, but ensure that you add the `--allow-insecure-unlock` option into the list of options for the `eth-node-1` node's `command:` within the `docker-compose.yml`.

## Wallet controls

The previous lab focused on watching the nodes' consensus algorithm behaviour, and how the nodes were intercommunicating. That is all activity that sits below the typical transactional use of blockchain: i.e., many cryptocurrency traders often will not be aware of, or indeed even really care about the details of the underlying software implementation.

The JavaScript console that was used in the previous lab to check balances can also take the place of a user interface for an Ethereum wallet.

Before discussing the steps involved in initiating transfer of funds, it is worth noting that the JavaScript console can be used to create convenience functions. For example, the code to display balances of all accounts can be wrapped in a function with a short name, for example `b`.

```javascript
var b = function() {
  eth.accounts.forEach(function(account) {
    console.log(account + ": " +
                web3.fromWei(eth.getBalance(account), "ether") +
                " ETH");
  });
}
```

After executing this code in the JavaScript console, then the function call `b()` will disply all of the balances.

### Transfering funds

The general shape of interaction will proceed as follows. Note that any code that contains angle brackets is likely to be prompting for data to be replaced with an appropriate specific value that you should determine.

In some contexts the sender's key needs to be imported. With a call such as:

```javascript
personal.importRawKey("<sender-private-key>", "<password>")
```
... which will return the public address as a hexadecimal string.

However, since our first node created the keys we're using, I've found that I can directly proceed to the next step, using the sender public addresses seen in the output of the function to print the account balances.

:::info
:bulb: The `<sender-private-key>` for an Ethereum account is a hexadecimal string that's 64-characters long (i.e., 256 bits). It is not written with the `0x` prefix that would be typical in C-style hexademical notation, it is just the sequence of hexadecimal digits (i.e., [0-9][a-f]).
:::

The account then needs to be unlocked.

```javascript
personal.unlockAccount("<sender-public-address>", "<password>")
```

This will then allow for a transaction to be requested for some given value.

```javascript
eth.sendTransaction({
    from: "<sender-public-address>",
    to: "<receiver-public-address>",
    value: web3.toWei(<amount-in-ether>, "ether")
})
```

For security the account should then be locked, with a construct such as the following---although in this lab we need not perform such actions, given that security has already been critically sacrificed in many places!
    
```javascript
personal.lockAccount("<sender-public-address>")
```

    

## Exercises

:::success
:pencil:
**Task** (recommended) Transfer funds from an account that you are able to unlock, and confirm that the balance of both affected accounts changes as expected. Note how long it takes before the transaction is committed.
:::

:::success
:pencil:
**Task** (recommended) Investigate what happens if you try to transfer more money than is present within the source account.
:::

:::success
:pencil: 
**Task** (optional) Determine how to implement and execute a smart contract beyond a transfer of "funds".
:::

:::success
:pencil: 
**Task** (very optional) See if you can download and run a local Ethereum wallet that can connect to your test network. (This is not likely to be possible on the Owheo Lab computers.)
:::

## Cleaning up

:::warning
:warning: Remember that the `/eth-data` created by running your Ethereum test will remain even after the containers are shut down. So after doing `docker-compose down` to shut down the containers, you additionally want to `rm` the directories `eth-node-1`, `eth-node-2` and `eth-node-3`
:::