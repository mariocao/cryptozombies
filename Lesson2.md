## Lesson 2 - Zombies attacks their victims

### More on data types

#### Addresses
The Ethereum blockchain is made up of accounts, which you can think of like bank accounts.

Each account has an **address**, which you can think of like a bank account number. It has an **unique Id** and looks like this
```
0x0cE446255506E92DF41614C46F1d6df9Cc969183
```

#### Mappings

A **mapping** is essentially a key-value store for storing and looking up data.
```
// For a financial app, storing a uint that holds the user's account balance:
mapping (address => uint) public accountBalance;
// Or could be used to store / lookup usernames based on userId
mapping (uint => string) userIdToName;
```

In the first example, the key is an address and the value is a uint, and in the second example the key is a uint and the value a string.

### Miscelanea

#### msg.sender

In Solidity, there are certain global variables that are available to all functions. One of these is **msg.sender**, which refers to the address of the person (or smart contract) who called the current function.

#### require

**require** makes it so that the function will throw an error and stop executing if some condition is not true:

#### Import

```
import "./someothercontract.sol";
```

### Inheritance

One feature of Solidity that makes this more manageable is **contract inheritance**:

```
contract Doge {
  function catchphrase() public returns (string) {
    return "So Wow CryptoDoge";
  }
}

contract BabyDoge is Doge {
  function anotherCatchphrase() public returns (string) {
    return "Such Moon BabyDoge";
  }
}
```

### Storage vs. Memory

In Solidity, there are two places you can store variables — in storage and in memory.

Solidity handles them by default:
 * State variables (variables declared outside of functions) are by default storage and written permanently to the blockchain
 * Variables declared inside functions are memory and will disappear when the function call ends.

However, there are times when you do need to use these keywords, namely when dealing with structs and arrays within functions:

```
contract SandwichFactory {
  struct Sandwich {
    string name;
    string status;
  }

  Sandwich[] sandwiches;

  function eatSandwich(uint _index) public {
    // Sandwich mySandwich = sandwiches[_index];

    // ^ Seems pretty straightforward, but solidity will give you a warning
    // telling you that you should explicitly declare `storage` or `memory` here.

    // So instead, you should declare with the `storage` keyword, like:
    Sandwich storage mySandwich = sandwiches[_index];
    // ...in which case `mySandwich` is a pointer to `sandwiches[_index]`
    // in storage, and...
    mySandwich.status = "Eaten!";
    // ...this will permanently change `sandwiches[_index]` on the blockchain.

    // If you just want a copy, you can use `memory`:
    Sandwich memory anotherSandwich = sandwiches[_index + 1];
    // ...in which case `anotherSandwich` will simply be a copy of the 
    // data in memory, and...
    anotherSandwich.status = "Eaten!";
    // ...will just modify the temporary variable and have no effect 
    // on `sandwiches[_index + 1]`. But you can do this:
    sandwiches[_index + 1] = anotherSandwich;
    // ...if you want to copy the changes back into blockchain storage.
  }
}
```

### More on Function visibility

Solidity has two more types of visibility for functions: internal and external.
* internal is the same as private, except that it's also accessible to contracts that inherit from this contract.
* external is similar to public, except that these functions can ONLY be called outside the contract.

```
contract Sandwich {
  uint private sandwichesEaten = 0;

  function eat() internal {
    sandwichesEaten++;
  }
}
```

### Interfaces

Interfaces are similar to abstract contracts, but they cannot have any functions implemented.

```
contract NumberInterface {
  function getNum(address _myAddress) public view returns (uint);
}
```

Notice that this looks like defining a contract, with a few differences:
 1. only declaring the functions we want to interact with and no mention of any of the other functions or state variables
 2. not defining the function bodies. Instead of curly braces ({ and }), simply ending the function declaration with a semi-colon (;).

For using an interface, the contract address is required:
```
contract MyContract {
  address NumberInterfaceAddress = 0xab38... 
  // ^ The address of the FavoriteNumber contract on Ethereum
  NumberInterface numberContract = NumberInterface(NumberInterfaceAddress);
  // Now `numberContract` is pointing to the other contract

  function someFunction() public {
    // Now we can call `getNum` from that contract:
    uint num = numberContract.getNum(msg.sender);
    // ...and do something with `num` here
  }
}
```

### Multiple return values

In solidity, it is posssible to return multiple values:
```
function multipleReturns() internal returns(uint a, uint b, uint c) {
  return (1, 2, 3);
}
```