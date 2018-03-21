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
- 如果定义为private不代表肉眼无法看到，只代表其他智能合约无法看到
#### 函数
- 四种都可以，默认public
- 关于external,作用：告诉合约未来的开发者，不希望此函数在合约内部使用.
```
contract Test{
 function func1 external (){
 }
 function func2(){}
  func1();
}
//此处error will be reported. 但是你可以用this.func1()来调用同一个合约中的其他函数.this 相当于把func1当作是外部函数来处理
//如果func1 是非external类型，this.func1() 和 func1（）都可以用，不过this.func1()会贵一些
```

### 继承

#### 
#### 基本语法

```
pragma solidity ^0.4.14;

contract owned {
    address owner;
    function owned(){
        owner = msg.sender;
    }

}


contract Parent is owned{
    // Parent inherit from owned;
    uint x;
    function Parent (uint _x){
        x = _x;
    }
    
    
    function parentFunc1() internal {
    
        if (msg.sender == owner) selfdestruct(owner);
    }
    
    function parentFunc2 () public {
        
    }
    
    function parentFunc3 () external {
        
    }
    
    function parentFunc4 () private {
        
    }
    
}

contract Child is Parent{
    
    uint y;
    function Child(uint _y) Parent(_y*y){
        y = _y;
    }
    //Parent（）zhe'li这里这里是shi这里due to parent class need the construction. Write down the parameter that parent need  
    
    function child(){
        parentFunc2();
        parentFunc1();
        this.parentFunc3();// you can only call parentFunc3 by this.
       // parentFunc4(); // you will see warning here
        
    }
    
}
    contract Child2 is Parent(666){
        //construct parent at the definitiohn of Child2 by passing a constant 666
        uint y;
        function Child2(uint _y){
            y = -y;
        }
    }
```

#### 抽象合约（这种合约是没有办法部署到区块链上的）
父类的某个函数没有定义
```
pragma solidity ^0.4.14;

contract owned {
    address owner;
    function owned(){
        owner = msg.sender;
    }

}


contract Parent is owned{
    // Parent inherit from owned;
    uint x;
    function SomeFunc () returns(uint);
    
}

contract Child is Parent{
    
    uint y;
    function SomeFunc() returns(uint){
        // SomeFunc is defined in children, not in parent: this is called Abstract contract. 
        return 1    ;
    }
}

```
#### 继承-Interface
像抽象合约，但本身离合约更远一步
跟抽象合约相比：
抽象合约可以有自己的状态变量，创造函数
但interface只是一个告诉之后编程人的框架，告诉大家要实现什么功能。只有function定义，剩下什么都没有
所以子类继承interface的时候，要求要把没有实现的函数全部实现，不能有虚函数存在（void），否则无法部署在虚拟机上。

```
pragma solidity ^0.4.14;

interface Parent{
    //不可继承其他合约或者Interface
    //没有构造函数
    //没有状态变量
    //没有struct
    //没有enum
    //简单说，只有function定义，啥都没有！
 function SomeFunc () returns(uint);
    
}

contract Child is Parent{
    
    uint y;
    function SomeFunc() returns(uint){
        return 1    ;
    }
}


```
#### 继承- 多继承
最复杂：
solidity其他不行，但这个倒是很完备
```
```
##### 最平凡的多继承

```
contract Base1{
    function Func1(){}
    
}

contract Base2{
    function Func2(){}
}
  
contract Final is Base1, Base2 {
    
}
```

##### 重名函数的overide次序
```
pragma solidity ^0.4.14;

contract Base1{
    function Func1(){}
    
}

contract Base2{
    function Func1(){}
}
  
contract Final is Base1, Base2 {
    
}

contract test{
    Final f = new Final();
    //在一个contract中创建一个新的contract
    f.Func1();
    //继承顺位是从后往前，因为Base2在后，所以调用的是Base2
}
```

##### super：动态绑定上级函数
```
pragma solidity ^0.4.14;

contract foundation{
    function func1(){
        //do something
    }
}

contract Base1{
    function func1(){super.func1();}
    
}

contract Base2{
    function func1(){super.func1();}
}
  
contract Final is Base1, Base2 {
    Final f = new Final();
    f.func1(); 
    //这里Func1调用时Base2里的func1，但Base2是调用的不是foundation的func1！
    //函数调用次序，Base2.func1,Base1.func1,foundation.func1
    //谁定义了这种线性结构？

}

```

##### super：动态绑定上级函数
假设下面所有合约都有一个同名函数，实现都为如下，函数调用的顺序是什么？
```
funciton foo(){
super.foo();
}
contract O
conract A is O
contract B is O
contract C is O
contract D is O
contract E is O
contract K1 is C, B ,A
contract K2 is E,B,D
contract K3 is A,D
contract Z is K3, K2, K1
//假设每一个合约里都有一个super.foo,那么到底是谁？
```
跟python一样，多继承method Resolution Order 使用C3Linearization： 网上搜，老董答疑
不能出现环路


# 问题
- 提问问题在哪里？
- 既然employee = employees[employeeId]是取得storage里的地址，那为啥在

function updateEmployee(address employeeId, uint salary) 中，

employee.lastPayday = now;
和
employees[employeeId].lastPayday = now 是一样的？

- 在讲完如何用mapping修改code之后，frank试了试update employee确实没有对gas产生影响，但是应该试一试增加成员后，再执行calculateRunway是否会有gas的变化？
