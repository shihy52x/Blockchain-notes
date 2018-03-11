# 第一课-简单的员工系统
实现员工的薪酬支付
## Hello world 智能合约

Hello world 智能合约

programa  solidity ^0.4.0

contract SimpleStorage {
  unit storageData;
  
  function set(unit x) {
    storagedata =x;
  }
  
  function get() constant returns(unit){
    return storageData; 
  }
}
