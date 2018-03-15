## 多员工支付系统
错误检测：
- assert(bool) 确认 一般是在程序运行当中查看是否出现运行错误
- require(bool) 要求 检测在程序输入的值满足要求
可以替换很多if语句。

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
        employee.transfer(salary);

    }
    
    function updateEmployee(address x, uint s){
        require(msg.sender == owner);
        if(employee != 0*0){
            uint payment = (salary * (now - lastPayday) / payDuration);
            employee.transfer(payment);
        }

        lastPayday = now;
        salary = s * 1 ether;
        employee = x;
    }
}
```
