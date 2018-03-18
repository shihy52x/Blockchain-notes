# 后端+前端 产品化

上次作业题里出现的同一个函数调用随着成员数的增加而增加，导致系统执行的成本越来越高。这节课来讨论解决办法
先引入一种新的数据结构

## MAPPING
- 类似于 MAP(C++), dict(python). key - value 的映射
- the value type for key: bool, int, address, string. 在solidity里，估计很长时间只能使用这四种类型做key
- value 可以是任何类型
- mapping(address=>Employee) employees
- mapping 只能做成员变量，不能做局部变量

### Mapping 的底层实现
- 不使用数组+链表，不需要扩容 （non permutable？） 在python中，如果是list，每增加一个成员，list的存储结构可能会发成变化.但是dict增加新成员不会
- hash函数为keccak256hash,在storage上储存，理论无限大的hash表。
- 缺点：无法naiive遍历整个mapping.所以在solidity里是没有这样的功能
 -赋值：employees[key] = value
 -取值：value = employees[key]
 -value 是引用，在storage，**可以直接修改：这跟 的dict差别很大！！ a=dict['x'] 那么对a的修改会影响dict的值**
 - 当key 不存在，value = type’s default： **这个跟python的dict也很不一样**
  
 同时把本地变量totalSalary变成成员变量（状态变量）。在有employee修改的的地方都要totalSalary 修改

## 函数参数返回进阶
- 命名参数返回
- 命名返回参数直接赋值
解决的问题：现在的code没办法很方便得到现在员工的信息：比如某一个员工的薪水，地址，lastPayday等等

```
function checkEmployee(address employeeId) returns(uint, uint) {
      var employee = employees[employeeId];
      return(employee.salary,employee.lastPayday);
    }
```
也可以不用return直接返回
```
function checkEmployee(address employeeId) returns(uint salary, uint lastPayday) {
        var employee = employees[employeeId];
        salary = employee.salary;
        lastPayday = employee.lastPayday;
    }
```

最终code

```
pragma solidity ^0.4.14;

contract Payroll {
    struct Employee {
        address id;
        uint salary;
        uint lastPayday;
    }
    
    uint constant payDuration = 10 seconds;
    uint totalSalary = 0;
    address owner;
    mapping (address=>Employee) employees;
    
    function Payroll() {
        owner = msg.sender;
    }
    
    function _partialPaid(Employee employee) private {
        if(employee.id !=0x0){
            uint payment = employee.salary * (now - employee.lastPayday) / payDuration;
            employee.id.transfer(payment);
        }
    }
    


    function addEmployee(address employeeId, uint salary) {
        require(msg.sender == owner);
        var employee  = employees[employeeId];
        assert(employee.id == 0x0);
        employees[employeeId] = Employee(employeeId, salary * 1 ether,now);
        totalSalary += salary * 1 ether;
    }
    
    function removeEmployee(address employeeId) {
        require(msg.sender == owner);
        var employee = employees[employeeId];
        assert(employee.id != 0x0);
        _partialPaid(employee);
        totalSalary -= employee.salary * 1 ether;

        delete employees[employeeId];

    }
    
    function updateEmployee(address employeeId, uint salary) {
        require(msg.sender == owner);
        var employee = employees[employeeId];
        assert(employee.id != 0x0);
        employees[employeeId].lastPayday = now;
        _partialPaid(employee);
        totalSalary += (salary - employee.salary) * 1 ether;
        employees[employeeId].salary = salary * 1 ether;

        
    }
    
    function checkEmployee(address employeeId) returns(uint salary, uint lastPayday) {
        var employee = employees[employeeId];
        salary = employee.salary;
        lastPayday = employee.lastPayday;
    }
    
    function addFund() payable returns (uint) {
        return this.balance;
    }
    
    function calculateRunway() returns (uint) {
        return this.balance / totalSalary;
    }
    
    function hasEnoughFund() returns (bool) {
        return this.calculateRunway()>0;
    }
    
    function getPaid() {
        var employee = employees[msg.sender];
        assert(employee.id != 0x0);
        assert(now - employee.lastPayday > payDuration);
        uint nextPayday = employee.lastPayday + payDuration;
        employees[msg.sender].lastPayday = nextPayday;
        employee.id.transfer(employee.salary);
        
    }
}

```



## 可视度和继承

### 可视度
- public 谁都可见
- external 只有"外部调用"可见：很少有程序员在其他语言用，但solidity有这个东西
- internal 外部调用不可见，内部和子类可见 像protected
- private 只有当前合约可见

**可以用在函数上，也可以用在成员变量上**
#### 状态变量的可视度 
- public， internal（default），private 
- 如果定义public相当于定义一个取值函数

# 问题
- 提问问题在哪里？
- 既然employee = employees[employeeId]是取得storage里的地址，那为啥在

function updateEmployee(address employeeId, uint salary) 中，

employee.lastPayday = now;
和
employees[employeeId].lastPayday = now 是一样的？

- 在讲完如何用mapping修改code之后，frank试了试update employee确实没有对gas产生影响，但是应该试一试增加成员后，再执行calculateRunway是否会有gas的变化？
