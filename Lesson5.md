## Lesson 5: ERC721 & Crypto-Collectibles

### Token basics

A token on Ethereum, such as ERC20, is basically just a smart contract that follows some common rules:
* transfer(address \_to, uint256 \_value)
* balanceOf(address \_owner)

And internally:
* mapping(address => uint256) balances

ERC721 tokens are not interchangeable since each one is assumed to be unique, and are not divisible.

### ERC721

Functions to be implemented in order to inherit from ERC721:

```
contract ERC721 {
  event Transfer(address indexed _from, address indexed _to, uint256 _tokenId);
  event Approval(address indexed _owner, address indexed _approved, uint256 _tokenId);

  function balanceOf(address _owner) public view returns (uint256 _balance);
  function ownerOf(uint256 _tokenId) public view returns (address _owner);
  function transfer(address _to, uint256 _tokenId) public;
  function approve(address _to, uint256 _tokenId) public;
  function takeOwnership(uint256 _tokenId) public;
}
```

ERC721 spec has 2 different ways to transfer tokens:
* The first way is the token's owner calls transfer with the address he wants to transfer to, and the \_tokenId of the token he wants to transfer.
* The second way is the token's owner first calls approve, and sends it the same info as above. The contract then stores who is approved to take a token, usually in a mapping (uint256 => address). Then when someone calls takeOwnership, the contract checks if that msg.sender is approved by the owner to take the token, and if so it transfers the token to him.

### Libraries

A library is a special type of contract in Solidity.

```
pragma solidity ^0.4.18;

library SafeMath {

  function add(uint256 a, uint256 b) internal pure returns (uint256) {
    uint256 c = a + b;
    assert(c >= a);
    return c;
  }

}
```

One of the things it is useful for is to attach functions to native data types.

```
import "./safemath.sol";

contract ZombieFactory is Ownable {
  using SafeMath for uint256;
}
```

SafeMath's add, sub, mul, and div are functions that do the basic 4 math operations, but throw an error if an overflow or underflow occurs.

The error is thrown trhough an assert. It is similar to require, where it will throw an error if false. The difference between assert and require is that require will refund the user the rest of their gas when a function fails, whereas assert will not.

### Contract security enhancements: Overflows and Underflows

One major security feature you should be aware of when writing smart contracts: Preventing overflows and underflows.

OpenZeppelin has created a library called SafeMath that prevents these issues by default.

```
using SafeMath for uint256;

uint256 a = 5;
uint256 b = a.add(3); // 5 + 3 = 8
uint256 c = a.mul(2); // 5 * 2 = 10
```

### Syntax for comments

Comments in solidity:

* Single line: //
* Multi-line: /_ text _/

The standard in the solidity community is to use **natspec**.
It includes some annotations such as:

* @title
* @author
* @notice: describes the contract to users
* @dev: extra details for developers
* @param: describes each parameter
* @return: describes return value

Example:

```
/// @title A contract for basic math operations
/// @author H4XF13LD MORRIS
/// @notice For now, this contract just adds a multiply function
contract Math {
  /// @notice Multiplies 2 numbers together
  /// @param x the first uint.
  /// @param y the second uint.
  /// @return z the product of (x * y)
  /// @dev This function does not currently check for overflows
  function multiply(uint x, uint y) returns (uint z) {
    // This is just a normal comment, and won't get picked up by natspec
    z = x * y;
  }
}
```
