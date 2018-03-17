## Lesson 4 - Zombie Battle System

### More on function modifiers

Quite a few function modifiers have been covered:
* Visibility modifiers
    * private: only callable from other functions inside the contract
    * internal: like private but can also be called by contracts that inherit from this one
    * external: can only be called outside the contract
    * public
* State modifiers
    * view: by running the function, no data will be saved/changed
    * pure: not save any data to the blockchain, but it also doesn't read any data from the blockchain
* Custom modifiers

#### payable modifier

**payable** functions are part of what makes Solidity and Ethereum so cool — they are a special type of function that can receive Ether.

Additional information related with payable modifier:
 * **msg.value** is a way to see how much Ether was sent to the contract,
 * **ether** is a built-in unit.

Example:
```
contract OnlineStore {
  function buySomething() external payable {
    // Check to make sure 0.001 ether was sent to the function call:
    require(msg.value == 0.001 ether);
    // If so, some logic to transfer the digital item to the caller of the function:
    transferThing(msg.sender);
  }
}
```

### Transfer Ethers

You can use transfer to send funds to any Ethereum address.

For example, you could have a function that transfers Ether back to the msg.sender if they overpaid for an item:
```
uint itemFee = 0.001 ether;
msg.sender.transfer(msg.value - itemFee);
```

### Random numbers by using hash

We could do something like the following to generate a random number:

```
// Generate a random number between 1 and 100:
uint randNonce = 0;
uint random = uint(keccak256(now, msg.sender, randNonce)) % 100;
randNonce++;
uint random2 = uint(keccak256(now, msg.sender, randNonce)) % 100;
```

### If-else statement

Like in javascript:
```
if (zombieCoins[msg.sender] > 100000000) {
  // You rich!!!
} else {
  // We require more ZombieCoins...
}
```