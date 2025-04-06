//BRIDGECONTRCT
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.13;
import {ERC20} from "@openzeppelin/contracts/token/ERC20/ERC20.sol";
import { IERC20 } from "@openzeppelin/contracts/token/ERC20/IERC20.sol";
import {Ownable} from "@openzeppelin/contracts/access/Ownable.sol";
contract BridgeETH is Ownable{
    uint256 public balance;
    address public tokenAddress;
    event Deposit(address indexed depositor,uint amount);
    mapping(address=>uint256)public pendingBalance;


    constructor(address _tokenAddress)Ownable(msg.sender){
         tokenAddress = _tokenAddress;
    }
    function lock(IERC20 _tokenAddress, uint256 _amount) public  onlyOwner{
        require(address(_tokenAddress) == tokenAddress);
        require(_tokenAddress.allowance(msg.sender, address(this)) >= _amount);
        require(_tokenAddress.transferFrom(msg.sender, address(this), _amount));
        emit Deposit(msg.sender, _amount);
    }

    function unlock(IERC20 _tokenAddress, uint256 _amount) public onlyOwner{
         require(pendingBalance[msg.sender] >= _amount);
         pendingBalance[msg.sender] -= _amount;
         _tokenAddress.transfer( msg.sender,  _amount);

    }
     function burnedOnOppositeChain(address payable userAccount, uint256 _amount) public onlyOwner {
        pendingBalance[userAccount] += _amount;
    }
    receive() external payable {}


}

//ERC20 TOKEN CONTRACT

// SPDX-License-Identifier: Unlicense
pragma solidity ^0.8.13;
import {ERC20} from "@openzeppelin/contracts/token/ERC20/ERC20.sol";
import {Ownable} from "@openzeppelin/contracts/access/Ownable.sol";
contract ShinuCoin is ERC20,Ownable {
    uint256 public number;
    constructor()ERC20("Shinu","Shi")Ownable(msg.sender){

    }
    function mint(address payable _to,uint amount) public onlyOwner {
        _mint(_to,amount);
    }
    receive() external payable {}


 }

