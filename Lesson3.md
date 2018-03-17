## Lesson 3 - Advanced Solidity Concepts

### More on Functions

Additional types of functions:
* **Constructors**: optional special function that has the same name as the contract. It will get executed only one time, when the contract is first created.
* **Function Modifiers**: Modifiers are kind of half-functions that are used to modify other functions, usually to check some requirements prior to execution. 

Modifier example:
```
modifier olderThan(uint _age, uint _userId) {
  require(age[_userId] >= _age);
  _;
}
```

Usage example:
```
// Must be older than 16 to drive a car (in the US, at least).
// We can call the `olderThan` modifier with arguments like so:
function driveCar(uint _userId) public olderThan(16, _userId) {
  // Some function logic
}
```

### Contract immutability

After deploying a contract to Ethereum, it’s immutable, which means that it can never be modified or updated again.

For that reason it is important to take into account several factors such as:
* External dependencies, e.g. allowing to set the address of an external contract.
* Ownable contracts, e.g. OpenZeppelin's Ownable contract with modifier **onlyOwner** 

```
modifier onlyOwner() {
    require(msg.sender == owner);
    _;
  }
```

```
contract MyContract is Ownable {
  event LaughManiacally(string laughter);

  // Note the usage of `onlyOwner` below:
  function likeABoss() external onlyOwner {
    LaughManiacally("Muahahahaha");
  }
}
```

### Ethereum Gas considerations

In Solidity, your users have to pay every time they execute a function on your DApp using a currency called gas. How much gas is required to execute a function depends on how complex that function's logic is.

#### Struct packing to save gas

Normally there's no benefit to using these sub-types because Solidity reserves 256 bits of storage regardless of the uint size. For example, using uint8 instead of uint (uint256) won't save you any gas.

But there's an exception to this: inside structs.

If you have multiple uints inside a struct, using a smaller-sized uint when possible will allow Solidity to pack these variables together to take up less storage. For example:

```
struct NormalStruct {
  uint a;
  uint b;
  uint c;
}

struct MiniMe {
  uint32 a;
  uint32 b;
  uint c;
}
```

#### View functions to save gas

**view** functions don't cost any gas when they're called externally by a user.

This is because view functions don't actually change anything on the blockchain – they only read the data. So marking a function with view tells web3.js that it only needs to query your local Ethereum node to run the function, and it doesn't actually have to create a transaction on the blockchain (which would need to be run on every single node, and cost gas).

#### In memory storage to save gas

In most programming languages, looping over large data sets is expensive. But in Solidity, this is way cheaper than using storage if it's in an **external view** function, since view functions don't cost your users any gas. (And gas costs your users real money!).

Declaring arrays in memory, by using the **memory** keyword with arrays to create a new array inside a function without needing to write anything to storage. The array will only exist until the end of the function call, and this is a lot cheaper gas-wise than updating an array in storage — free if it's a view function called externally.

Here's how to declare an array in memory:
```
function getArray() external pure returns(uint[]) {
  // Instantiate a new array in memory with a length of 3
  uint[] memory values = new uint[](3);
  // Add some values to it
  values.push(1);
  values.push(2);
  values.push(3);
  // Return the array
  return values;
}
```


### Time Units

Solidity provides some native units for dealing with time.

The variable **now** will return the current unix timestamp (the number of seconds that have passed since January 1st 1970). 

Solidity also contains the time units:
 * seconds,
 * minutes,
 * hours,
 * days,
 * weeks and 
 * years.

For example:
```
uint lastUpdated;

// Set `lastUpdated` to `now`
function updateTimestamp() public {
  lastUpdated = now;
}

// Will return `true` if 5 minutes have passed since `updateTimestamp` was 
// called, `false` if 5 minutes have not passed
function fiveMinutesHavePassed() public view returns (bool) {
  return (now >= (lastUpdated + 5 minutes));
}
```

### For loops

The syntax of for loops in Solidity is similar to JavaScript.

Example:
```
function getEvens() pure external returns(uint[]) {
  uint[] memory evens = new uint[](5);
  // Keep track of the index in the new array:
  uint counter = 0;
  // Iterate 1 through 10 with a for loop:
  for (uint i = 1; i <= 10; i++) {
    // If `i` is even...
    if (i % 2 == 0) {
      // Add it to our array
      evens[counter] = i;
      // Increment counter to the next empty index in `evens`:
      counter++;
    }
  }
  return evens;
}
```