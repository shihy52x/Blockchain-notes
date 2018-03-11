# 第一课-简单的员工系统
实现员工的薪酬支付
## Hello world 智能合约

 Hello world 智能合约
 
```  
#程序版本，常用的语言，如c++，会在编译器里声明版本。而在solidity的code里声明版本，不是为了技术上的考虑，是为了开源社区协作互动的考虑
#声明一个合约，很像类的声明一样。一个合约就是一个封装单位，类，包涵了很多的method和variable
#unit 就是状态变量（整型变量），set 和get是method
pragma  solidity ^0.4.0;

contract SimpleStorage {
  uint storageData;
  
  function set(uint x) {
    storageData =x;
  }
  
  #constant 这里表示这个函数是个常成员函数，这个函数是不能更改成员变量的。在这里，constant是没有用的，毕竟
  #这个函数里没有任何修改的操作，只是起到一个视觉警示的作用。编译器是不会做任何检查的。
  #return unit 这里是定义返回的数据类型
 function get() constant returns(uint){
    return storageData; 
  }
}
```

## 在线IDE的practice-课程前半段使用
在线IDE地址https://remix.ethereum.org/
在没有链接前后端的时候，这个IDE是比较好用的，做快速的迭代和开发

打开之后，右面的面板，run section我们今天使用 JavaScript VM
说明我们run code的时候，并不是把这个合约放到真正的以太坊的区块链上，而是进行一个虚拟的测试
点击create之后我们就把合约部署到了虚拟的以太坊的开发环境当中

### gas conception:
After you "create" and click set, you will see a “detail” button in the informaiotn window. expand this button and you will see a table with  "gas" consumption.

执行一个智能合约的函数调用，所用的计算资源是由矿工（共同维护）提供的，支付的单位就是gas。那么问题来了？为什么我们不直接用ether去做支付呢？
假如现在这个funciton值1 ether，但是等ether 涨1000倍之后，函数的费用就涨1000？当然不是。我们希望把函数的价值。gas的price实用etherm来衡量的。通过变动gas的价格，可以使得函数的支付维持稳定。


## 背景介绍
- 传统的员工系统
- 问题
 - 成本比较高
 -  对小公司：信用（拖欠公司）
 
 目标：
 - 高效低成本
 - 防止违约，拖欠工资
 
## 类型系统
 静态类型：要申明，像c，不像python
 
 ### 布尔型：BOOL
 - ture
 - false
 - 操作符
  - ！
  - &&
  - ||
  - ==
  - ~=
  ### 整型 UINT/INT
   - 操作符：
    - 比较：<=,<,==,!=,>=,>
    - 位操作 : &,|,~,>>,<<
    - 计算： + - * / % **
  注意，在solidity里，目前编译器中是没有floating的！！
  
  ### 地址ADDRESS
  是solidity中一种原生的数据类型，像python中的dictionary一样也是数据类型。
  在数字货币里有各种各样的地址，智能合约本身也是有个地址，叫contract address
  地址有几个重要的成员变量和函数
  - address.balance - 成员变量：地址里面还有多少钱
  - address.transfer(value)：成员函数，后面课程会着重讲。transfer
  - address.send(value)，返回一个布尔值，表示call 是否成功
  - address.call, address.callcode and addresss.delegatecall： 更多会用在
 
 
