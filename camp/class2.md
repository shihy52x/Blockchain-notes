## 多员工支付系统
错误检测：
- assert(bool) 确认 一般是在程序运行当中查看是否出现运行错误
- require(bool) 要求 检测在程序输入的值满足要求
可以替换很多if语句

### revert 和 require 的使用：

````
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
````

### 支持多人的多员工系统

#### 数组
- 固定长度的数组(uint[5] a)
- 动态长度 (uint[] a) 长度可以改变
- 成员
 - length
 - push 适用于动态数组，添加成员
 
### Test of static array
 
 ````
 pragma  solidity ^0.4.14;

contract Test{
    uint[2] a;
    
    function test()returns(uint, uint){
        a[0] = 1 ;
        a[1] = 2 ;
        return (a[0],a[1]);
    }
    
}
 ````
 
### Test of dynamic array

 ````
pragma  solidity ^0.4.14;

contract Test{
    uint[] a;
    
    function test()returns(uint, uint,uint){
       a.push(1);
       a.push(2);
       
        return (a[0],a[1],a.length);
    }
    
}

 ````
## 设计
- 通过数组储存多个员工的信息：
    - address[] employee
    - uint[] salary
    - uint[] lastPayday
- Struct

````
  struct Employee {
    address id;
    uint salary;
    uint lastPayday;
  }
  
  Employee frank;
  ````
  
  ## 数据存储 DATA LOCATIOn
 
 - storage: EVM上一个永久性的空间，在区块儿链上
 - memory： 在EVM上一个临时的空间，function运行完后就消失
 - call data，类似于memory，也会在function 运行完后消失。
 
 强制
 -状态变量一定在storage
 - function 输入参数：call data
 
 默认
 -function 返回参数：memory
 - 本地变量 storage
 
 规则
 - 相同存储空间赋值，传递reference（EVM上的内存地址，像指针）
 - 对于不同存储空间的变量赋值：复杂
    - copy Employee employee = findEmployee(EmployeeId)，这个function返回的是一个memory的地址，在funciton中，storage没有传递reference给employee，而是copy了一份到memory中，那么现在对employee的任何操作都不会影响employees了
    -
    
```` 
 pragma  solidity ^0.4.14;

contract Payroll{
    struct Employee {
        address id;
        uint salary;
        uint lastPayday;
    }
    
    address owner ;
    Employee[] employees ;
    uint constant  payDuration = 10 seconds;
// this is  a comment 

    function Payroll(){
        owner = msg.sender;
    }
    function addfund() payable returns(uint){
        return this.balance;
        
    }
    function calculateRunway() returns (uint) {
        uint totalSalary = 0;
        for (uint i =0; i < employees.length; i++){
            totalSalary += employees[i].salary;
        }
        return this.balance / totalSalary;

    }
    
    function hasEnoughfund() returns(bool){
        return this.calculateRunway() > 0;
    }
    
    function gatPaid(address employeeId){
        require(msg.sender == employeeId);
        var(employee,index) = _findEmployee(employeeId);
        assert(employee.id != 0x0);
        uint nextPayday = employee.lastPayday + payDuration;
        assert(nextPayday < now);
        employee.lastPayday = nextPayday;
        employee.id.transfer(employee.salary);
    }
    
    function addEmployee(address employeeId, uint s){
        require(msg.sender == owner);
        var(employee,index) = _findEmployee(employeeId);
        assert(employee.id ==0x0);
        employees.push(Employee(employeeId,s,now));
    }
    
    function rmEmployee(address employeeId){
        require(msg.sender == owner);
        var (employee,index)  = _findEmployee(employeeId);
        assert(employee.id != 0x0);
        _partialPaid(employee);
        delete employees[index];
        employees[index] = employees[employees.length-1];
        employees.length -= 1;

    }
    
    function _partialPaid  (Employee employee) private{
        if (employee.id != 0X0){
            uint payment = employee.salary * ((now - employee.lastPayday)/payDuration);
        }
    }
    
    function _findEmployee(address employeeId) private returns(Employee, uint) {
        for(uint i = 0; i < employees.length; i++){
            if (employees[i].id == employeeId){
                return (employees[i],i);
            }
        }
    }
    
    function updateEmployee(address employeeId, uint s){
        require(msg.sender == owner);
        var (employee,index) = _findEmployee(employeeId);
        assert(employee.id != 0X0);
        _partialPaid(employee);
        employees[index].lastPayday = now;
        employees[index].salary =s;

        }

    
    
}

````
