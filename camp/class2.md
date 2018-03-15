## 多员工支付系统
错误检测：
- assert(bool) 确认 一般是在程序运行当中查看是否出现运行错误
- require(bool) 要求 检测在程序输入的值满足要求
可以替换很多if语句

### revert 和 require 的使用：

```
pragma  solidity ^0.4.14;

contract Payroll{
    uint salary = 1 wei;
    address owner ;
    address employee ;
    uint constant  payDuration = 10 seconds;
    uint lastPayday = now;
// this is  a comment 

    function Payroll(){
        owner = msg.sender;
    }
    function addfund() payable returns(uint){
        return this.balance;
        
    }
    function calculateRunway() returns (uint) {
        return this.balance / salary;
    }
    
    function hasEnoughfund() returns(bool){
        return this.calculateRunway() > 0;
    }
    function gatPaid(){
        require(msg.sender == employee);
        uint nextPayday = lastPayday + payDuration;
        assert(nextPayday < now);
        lastPayday = nextPayday;
        employee.transfer(salary);

    }
    
    function updateEmployee(address x, uint s){
        require(msg.sender == owner);
        if(employee != 0x0){
            uint payment = (salary * (now - lastPayday) / payDuration);
            employee.transfer(payment);
        }

        lastPayday = now;
        salary = s;
        employee = x;
    }
}
```
### 支持多人的多员工系统

#### 数组
- 固定长度的数组(uint[5] a)
- 动态长度 (uint[] a) 长度可以改变
- 成员
 - length
 - push 适用于动态数组，添加成员
 
 Test of static array
 
 ```
 pragma  solidity ^0.4.14;

contract Test{
    uint[2] a;
    
    function test()returns(uint, uint){
        a[0] = 1 ;
        a[1] = 2 ;
        return (a[0],a[1]);
    }
    
}
 ```
 
 Test of dynamic array

 ```
pragma  solidity ^0.4.14;

contract Test{
    uint[] a;
    
    function test()returns(uint, uint,uint){
       a.push(1);
       a.push(2);
       
        return (a[0],a[1],a.length);
    }
    
}

 ```
