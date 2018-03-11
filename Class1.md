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
