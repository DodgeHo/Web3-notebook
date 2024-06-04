    // SPDX-License-Identifier: MIT
    pragma solidity 0.8.17;
    
    contract MyToken {
      //mapping used to store the balance corresponding to each address
      mapping (address => uint256) private balances;
      //A uint256 variable is used to store the total supply of the token. It is defined as public and can be queried by anyone.
      uint256 public totalSupply;
      //An address variable is used to store the issuer of this token. This is used for some permission control.
      address private owner;
    
      constructor(uint256 initialSupply) {
    owner = msg.sender;
    mint(msg.sender, initialSupply);
      }
      //function used to mint tokens
      function mint(address recipient, uint256 amount) public {
    //Permission control is implemented
    require(msg.sender == owner,"Only the owner can perform this action");
    balances[recipient] += amount;
    totalSupply += amount;
    }
      //A function used to query the balance corresponding to an address
      function balanceOf(address account) public view returns (uint256) {
    return balances[account];
      }
      //A function used to transfer
      function transfer(address recipient, uint256 amount) public returns (bool) {
    require(amount <= balances[msg.sender], "Not enough balance.");
    balances[msg.sender] -= amount;
    balances[recipient] += amount;
    return true;
      }
    }


