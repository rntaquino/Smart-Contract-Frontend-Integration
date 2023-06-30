# Smart-Contract-Frontend-Integration
Integrating Smart Contract -  This repository will showcase 2 functions being deployed to a frontend application.
## Getting Started
The file below allows a smart contract to deposit a certain amount of ETH from the sender address to the receiver address in Metamask:

```Java
// SPDX-License-Identifier: UNLICENSED
pragma solidity ^0.8.9;

//import "hardhat/console.sol";

contract Assessment {
    address payable public owner;
    uint256 public balance;

    event Deposit(uint256 amount);
    event Withdraw(uint256 amount);
    event Transfer(address sender, address receiver, uint256 amount);

    constructor(uint initBalance) payable {
        owner = payable(msg.sender);
        balance = initBalance;
    }

    function getBalance() public view returns (uint256) {
        return balance;
    }

    function deposit(uint256 _amount) public payable {
        uint _previousBalance = balance;

        // make sure this is the owner
        require(msg.sender == owner, "You are not the owner of this account");

        // perform transaction
        balance += _amount;

        // assert transaction completed successfully
        assert(balance == _previousBalance + _amount);

        // emit the event
        emit Deposit(_amount);
    }

    // custom error
    error InsufficientBalance(uint256 balance, uint256 withdrawAmount);

    function withdraw(uint256 _withdrawAmount) public {
        require(msg.sender == owner, "You are not the owner of this account");
        uint _previousBalance = balance;
        if (balance < _withdrawAmount) {
            revert InsufficientBalance({
                balance: balance,
                withdrawAmount: _withdrawAmount
            });
        }

        // withdraw the given amount
        balance -= _withdrawAmount;

        // assert the balance is correct
        assert(balance == (_previousBalance - _withdrawAmount));

        // emit the event
        emit Withdraw(_withdrawAmount);
    }

    function transferSingleEth(address _receiver) public payable {
        require(
            msg.sender != _receiver,
            "Receiver address should be different from sender address"
        );
        require(msg.value == 1 ether, "Amount should not be 1 ether");

        (bool status, ) = payable(_receiver).call{value: msg.value}("");
        if (!status) {
            revert("Failed sending ether to receiver address");
        }

        // emit the event
        emit Transfer(msg.sender, _receiver, msg.value);
    }

    function transferEth(address _receiver) public payable {
        require(
            msg.sender != _receiver,
            "Receiver address should be different from sender address"
        );
        require(msg.value != 0, "Amount should not be zero");

        (bool status, ) = payable(_receiver).call{value: msg.value}("");
        if (!status) {
            revert("Failed sending ether to receiver address");
        }

        // emit the event
        emit Transfer(msg.sender, _receiver, msg.value);
    }
}

```
## Executing the Program

Deploy the contract at: https://remix.ethereum.org/#lang=en&optimize=false&runs=200&evmVersion=null&version=soljson-v0.8.18+commit.87f61d96.js

Connect your Metamask to Polygon PoS Chain Testnet Explorer using this website: https://mumbai.polygonscan.com/

To accumulate funds for the transactions, create multiple accounts and add funds to the account using this website: https://faucet.polygon.technology/
Do note that the website only allows one transaction per account receiving only 0.2 MATIC, so once accounts have received 0.2 MATIC each, transfer them to a single account to be able to use the first function, "transferSingleEth," which requires 1 ETH to transact.

Once everything is set up, run the code from your visual code on the desktop and launch the application, which should be at http://localhost:3000/

In the frontend of the application, there would be two functions:

### For the function transferSingleEth:

```Java

    function transferSingleEth(address _receiver) public payable {
        require(
            msg.sender != _receiver,
            "Receiver address should be different from sender address"
        );
        require(msg.value == 1 ether, "Amount should not be 1 ether");

        (bool status, ) = payable(_receiver).call{value: msg.value}("");
        if (!status) {
            revert("Failed sending ether to receiver address");
        }

        // emit the event
        emit Transfer(msg.sender, _receiver, msg.value);
    }
```
This function will transfer 1 Ether to a specified receiver address. We also added some statements, such as the receiver and sender address should be different, and it will revert the process if it fails to send ether to the receiver address.

### For the function transferEth:

```Java

    function transferEth(address _receiver) public payable {
        require(
            msg.sender != _receiver,
            "Receiver address should be different from sender address"
        );
        require(msg.value != 0, "Amount should not be zero");

        (bool status, ) = payable(_receiver).call{value: msg.value}("");
        if (!status) {
            revert("Failed sending ether to receiver address");
        }

        // emit the event
        emit Transfer(msg.sender, _receiver, msg.value);
    }
}
```

We will use this function to transfer a specified amount of Ether to the provided receiver address. Same as the previous functions, we have statements that the receiver and sender address should be different, and revert the process if it fails to send ether to the receiver address.
