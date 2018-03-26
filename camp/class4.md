# TRUFFLE 全栈开发
Ethrum生态系统中最大的开发框架

- 编译，部署到eth网络上
- 进行solidity程序测试
- 包管理：zeppeli：安装加载
- 前段开发

## 开发环境
为了统一大家的开发环境，用配置好的虚拟机：vitual box

## 主要命令
- truffle compile
- trfulle migrate
- console
- test
- unbox

abi application binary interface: solidity规定的标准方式，与外界交互
abi知道哪些函数可以调用


## 部署（deploy）
### 客户端
如果用到guss paras，都是基于实际的ethrem网络，成本高，不利于开发测试，2 出块儿时间慢
所以Truffle有另一个客户端，基于javascript模拟器，不需要挖苦 
视频让搜索：testrpc
实际要搜索ganache-cli

- mkdir payroll
- cd payroll
- truffle Init #创建新的合约
  - contract/ #存放智能合约
   - ConvertLib.sol 是被metacoin用到的lib
    - 汇率转换的一个小funciton
   - metacoin.sol 合约
    - 调用convertLib
    - sendCoin(address receiver, uint amount)
    - getblance(address addr)
   - Migration.sol 部署合约的时候用到的？
     - 一般不需要改
  - migration #存放帮智能合约deploy到网络上的文件
    - initial_migration.js 不需要改，用于deploy migration这个文件
    - deploy_contracts.js . 
      - demployer.delopy (ConvertLib) 将convertLib 发布到etherum 网上
      - deployer.link (ConvertLib, MetaCoin) 把已经发布的convertLib链接到智能合约上,这样可以metacoin里实用convertLib
      - deloyer.deploy(Metacoin)将Metacoin发布到etherum上
  - test # 将智能合约进行测试，检查是否有错，后期会详细讲解
  - truffle.js #配置信息，将智能合约部署到相对应的网络上
    - network 网络配置信息
    - development 用于开发
    -
### 主要命令
- truffle compile
 - 将contracts/ 下面三个文件进行编译 create build/contracts/ 里面有三个jason文件. 当contract 部署的区块链上的时候，产生的接口方便大家进行交互：ABI
 - 打开新的tab，testrpc
- truffle migrate
 - truffle migration #运行在migration文件夹里定义的程序。 将智能合约部署到testrpc的network
- truffle console
  - 通过 truffle console 跟已经deploy 到network上的contract进行交互
   - MetaCoin.depolyed().then(contract =>{metacoin=contract}) 这是异步方程，用javascript里面的promise？将结果赋值给metacoin这个变量. contract 实例
  metacoin.getBalance.call(adress A).then(result=>{console.log(result)})
  //
  metacoin.sendCoin.call(addressB,2000).then(result=>{console.log(result)})
  //这个call虽然send了2000到这个地址，但是因为用的是call()，随意，只会修改本地node的值，没有保存在区块链上 
    metacoin.sendCoin(addressB,100).then(result=>{console.log(result)})
  //这个是发送到了区块链上了，返回信息是trasaction的具体的信息这时，addressA里面的balance减少100，而addresssB增加100

- truffle test
- truffle unbox

### 实用Truffle console来和进行interactive
## WEB3.js
- 一个javascript和区块链交互的桥梁
  - web3使用智能合约的API来获取智能合约中有什么样的方程可以调用
  - 也可以在前端和区块链进行交互
 -web3.eth.accounts: 列出当前testrpc网络中所有的账户
 -web3.currentProvider 列出当前etherum网络的一些信息
 -web3.personol.
  -web3.personol.newAccount() 创建新的账户
- web3.setttings.
 - web3.settings. defaultAccount
- web3.eth.  重要
 - web3.eth.getbalance 查看账户余额
 - 查看gas price
 
 ### web3调用合约方程
 - 方程名.call() 不能修改状态变量
 -     
 方程名.send()或者方程名()
 步骤 

 -  MetaCoin.depolyed().then(contract =>{metacoin=contract}) 将结果赋值给metacoin这个变量
 - web3.eth.accounts 查看合约中存在那些账户地址
 - 随便选一个 metacoin.getBalance.call()
 web3. + TAB 列出可用的method
 
## 前段开发并发布只能合约
- truffle box
truffle里面已经准备好的脚手架，不仅有一些contract，还有一些前段的代码，可以使用直接开发前段应用
使用web3 javascript library
下载方法：
http://truffleframework.com/boxes/react
而因为我们的virtual box 已经安装好了，所以直接用就好

- config/ 前段构建需要的配置文件
- node_modules 前段的依赖文件
- public 图片或者静态文件
- script 前段运行需要的一些程序
- src 前段的源代码，前段开发的时候主要写代码的地方
- package jason 前段开发
- contracts
 - SimpleStorage.sol 
- migration

运行前段的步骤
cd react/
1 truffle compile
2 truffle migrate or trffle migration
3 npm run start 
4 进入浏览器，输入 localhost:3000 
6 
 - 到 src/css font size set up. pure-min.css： 前段框架的文件： 雅虎的开源的前段框架 ./fonts/ 字体 
 - utils/getWeb3.js: 用来获取web3实例的程序
 - index.js React 程序的入口（可以去查看官方网站），可以非常方便的构建前段的程序。需要自己学习开发的相关的知识
 - app.js 存了很多我们之前在console mode里面的很多的操作
   - 修改simpleStorageInstance.set(10, {from: accounts[0]}) 里面的值，然后save，后端就会自动的compile，浏览器中的数值也会改变
   
   ## 如何测试
   在智能合约中尤其重要：跟钱相关，很多操作是不可逆的
   之前我们有很多的手动的测试，人多了之后测试不理想，浪费时间。所以需要自动化进行测试
   truffle提供了两种测试的方法：
   - js
      -Mocha
      -Chai
   -Solidity
   
 ### test/simplestorage.js
 
 ```
 contract('SimpleStorage', function(accounts) {
// 跟java中的describe（）很像？ 创建一个clean 的环境进行测试

var SimpleStorage = artifacts.require("./SimpleStorage.sol");

contract('SimpleStorage', function(accounts) {

  it("...should store the value 89.", function() {
  // it 关键词在这里描述我们要做的测试
    return SimpleStorage.deployed().then(function(instance) {
   //获取当前已经部署的simplestrorage的instance
   simpleStorageInstance = instance;

      return simpleStorageInstance.set(89, {from: accounts[0]});
      // 将数值设为89
    }).then(function() {
      return simpleStorageInstance.get.call();
    }).then(function(storedData) {
    //获取新的数值
      assert.equal(storedData, 89, "The value 89 was not stored.");
    });
  });

});
```
### 用solidity 写测试
/test/TestSimpleStrorage.sol
```
pragma solidity ^0.4.2;

import "truffle/Assert.sol";
import "truffle/DeployedAddresses.sol";
import "../contracts/SimpleStorage.sol";
//truffle 提供的辅助的测试的合约
contract TestSimpleStorage {
//强制明明规则：必须以Test开头 for contract，function里要以test开头
  function testItStoresAValue() {
    SimpleStorage simpleStorage = SimpleStorage(DeployedAddresses.SimpleStorage());
   //获取当前simpleStorage的地址，获得一个instance：例子
   simpleStorage.set(89);

    uint expected = 89;

    Assert.equal(simpleStorage.get(), expected, "It should store the value 89.");
  }

}

```
### 比较两种测试方法 javascript 和solidity 对比？
 - Solidity 优点：简洁（适合小的单元测试）
  -javascript 用了8行，而solidity只用用了3行，主要原因是javascript中要依赖异步执行，而solidity可以直接拿到结果
  -java script只能调用public的external的function，而solidity可以调用internal的函数
 - javascript优点：
  - javascript中的测试跟前段的测试基本相似，所以可以用javascript来模拟前段的使用：这种叫做整合式的测试
  - 用havascript来写测试还可以有强大的语法
  - javascript中可以简单实现程序异常的补充，solidity中没有太好的解决方案。虽然truffle提供了一些方法，但还是比较麻烦
所以结论是：推荐大家用javascript写test.而solidity可以简单写一些小的单元测试，来覆盖边缘的条件


###怎么测试?
修改完simplesrorage.js 或者 TestSimpleStorage.sol之后，返回terminal，运行 truffle test
则test中的两个文件会进行编译并运行


##总结：
- 安装truffle 
- truffle init 初始化一个项目
- truffle compile 编译智能合约
- truffle migrate 将智能合约部署到etherum网络： 学些了testrpc工具代替传统etherum客户端来快速开发程序
- truffle console 和部署的智能合约进行交互
- truffole test
- truffle unbox 下载一个已经配置好的前段环境来开发前段应用
- truffle 前段应用开发
- 下节课开发员工系统的开发程序



Test:

```
var SimpleStorage = artifacts.require("./SimpleStorage.sol")
//当然这个地方要用到SimpeStorage这个智能合约里的function
contract('SimpleStorage', function(accounts) {
//accounts的值应该是从web3里定义的，是个数组.而这个是要传入到下面的
  it("...should store the value 89.", function() {
    return SimpleStorage.deployed().then(function(instance) {
      simpleStorageInstance = instance;
      //这里instance是部署了之后产生的，需要在这个测试文件里赋值给simpleStorageInstance这个变量，后面就可以用simpleStorageInstance来进行操作了
      return simpleStorageInstance.set(89, {from: accounts[0]});
      //这里调用了set对第一个account进行设置，返回的是True而已，所以返回值不会用到下一步，而你会看到下面那个function的参数是空的
    }).then(function() {这里
      return simpleStorageInstance.get.call();
      //这里get这个函数会返回storeData，而这个storeData又会返回给web3的变量，接着就传到下一步function里面
    }).then(function(storedData) {
      assert.equal(storedData, 89, "The value 89 was not stored.");
    });
  });

});

```
```
var MetaCoin = artifacts.require("./MetaCoin.sol");

contract('MetaCoin', function(accounts) {
  it("should put 10000 MetaCoin in the first account", function() {
    return MetaCoin.deployed().then(function(instance) {
    //这里web3产生了一个instance
      return instance.getBalance.call(accounts[0]);
      //这个instance没有赋给哪个变量，可以直接用instance.XX来调用method,getbalance返回的是balances[address]-balanceobject,这个balance object会直接传给下一个function
      }).then(function(balance) {
      assert.equal(balance.valueOf(), 10000, "10000 wasn't in the first account");
      //调用balance里的值
    });
  });
  
  //再来第二个it
  it("should call a function that depends on a linked library", function() {
    var meta;
    var metaCoinBalance;
    var metaCoinEthBalance;
   //在it中定义几个对这个it的变量
    return MetaCoin.deployed().then(function(instance) {
      meta = instance;
      //这个instance定义给了meta,这样后面的function也可以调用meta了
      return meta.getBalance.call(accounts[0]);
      //返回的是一个balance的object,可以传给下面用-叫outCoinBalance
    }).then(function(outCoinBalance) {
      metaCoinBalance = outCoinBalance.toNumber();
      // 这个outCoinBalance是个balanceobject？
      return meta.getBalanceInEth.call(accounts[0]);
    }).then(function(outCoinBalanceEth) {
      metaCoinEthBalance = outCoinBalanceEth.toNumber();
    }).then(function() {
      assert.equal(metaCoinEthBalance, 2 * metaCoinBalance, "Library function returned unexpected function, linkage may be broken");
    });
  });
  
  //再来一个it
  it("should send coin correctly", function() {
    var meta;

    // Get initial balances of first and second account.
    var account_one = accounts[0];
    var account_two = accounts[1];
    var account_one_starting_balance;
    var account_two_starting_balance;
    var account_one_ending_balance;
    var account_two_ending_balance;

    var amount = 10;
// 这个amount 会用在下面不同的function里面
    return MetaCoin.deployed().then(function(instance) {
      meta = instance;
 return meta.getBalance.call(account_one);
 //返回balance传给下面的funciton
    }).then(function(balance) {
      account_one_starting_balance = balance.toNumber();
      return meta.getBalance.call(account_two);
      // balance原来是个stringobject，转换成number，返回第二个balance传到下面
    }).then(function(balance) {
      account_two_starting_balance = balance.toNumber();
      return meta.sendCoin(account_two, amount, {from: account_one});
      //得到第二个balance后，进行一个sendCoin的操作，把第一个账户的钱转到第二个账户.
    }).then(function() {
      return meta.getBalance.call(account_one);
      //这里account_one 是upperlevel的变量，所以可以直接用，返回一个string object，传到下面
    }).then(function(balance) {
      account_one_ending_balance = balance.toNumber();
      //这里得到一个balance的值再次传入下面
      return meta.getBalance.call(account_two);
    }).then(function(balance) {
      account_two_ending_balance = balance.toNumber();
      //进行一个asert操作
      assert.equal(account_one_ending_balance, account_one_starting_balance - amount, "Amount wasn't correctly taken from the sender");
      assert.equal(account_two_ending_balance, account_two_starting_balance + amount, "Amount wasn't correctly sent to the receiver");
    });
  });
});



```

# 加餐内容

### 合约A部署到以太坊网络中后，A可以调用已经在网络中部署好的合约B的代码吗，可以的话怎么调用？ （假设B是另外一个公司写的合约代码）
- 答案是可以调用，只要B的合约是public的
- 调用有两种方法
 - 使用call函数，调用过程中，调用的返回一定只能是bool，调用成功或者失败，funciton本身返回值是无法拿到的，原因是以太坊的value无法定义多长
 - 方法2，B合约写个骨架，然后在A中调用，
 - 如果真的想拿B的return结果，要接入汇编，比较危险的操作，现在不涉及
 
 ### 2. 合约D引用了Library C的代码，那么这个Library C是需要跟D一起静态编译后再部署到主网。还是C可以被别人事前部署到主网，这样我们不需要编译C的代码，D部署到主网后只需要运行时调用C，像动态链接一样
 
 C是可以是可以先部署到主网上的，
 library的坏处: library不能直接access本身的class的data的
 代码的真正重用可以实现吗？
 比如一个多签名钱包，写一次之后，不想让大家再写，再部署，怎么实现
 有一个共同的函数，汇编写的，放到区块链上，把代码考到本地的栈上，但是这个copy是有风险的，比如去年parity的多签名钱包被攻击，攻击者发现很多合约在copy一个单一的代码，而不是重新部署，destruct函数没有被屏蔽到。于是github有个user就修改了一下，把原合约杀掉了。于是新用户拷贝的东西是一个空指针0x0。所有资金都被锁在了一个很傻的合约中。就是想共享code操作出现了失误
 ### web3借口具体命令很多，有案例可以参考吗？
 web3的接口实际上是etherem api的完整的映射，在javascript中的完整的映射，是etherum full node（不是nodejs） 做的一个包装，我觉得最好的reference是etherum本身的api的reference
 https://github.com/ethereum/wiki/wiki/JSON-RPC#json-rpc-methods
 这个链接中可以看到每一个etherum的api都有详细解释，所以web3文档做的差，是完整映射
 ### frank说的方程是指function吗？不是的话，有什么区别？
 
 ### truffle 测试时候，怎么切换使用者地址
 在使用js测试的时候，可以定义from address来change msg.sender
 meta.sendCoin(account_two,account,{from:account_one});
 payroll.getPaid({from:account_one});
 
 ### 你一开始有增加员工的地址么？这里我忘记·“”· 需不需要了，理论上是不会出错的，如果有问题我们可以offline讨论一下，看看能不能reproduce
: 问 对，就是这个函数，怎么都报错不是员工账号
 
 ### contract 的 lifecycle
 再部署区块链的时候，底层到底发生了什么？我们
 我们在部署合约的时候，我们发的binary code不光是以后在区块链上运行的一段binary code，与此同时也是部署的binary code，相当于用代码generate在区块链中部署的代码。可以认为是function call，在contract create的时候可以看到有execution cost，就是因为创建的过程也是程序执行的步骤
 

### 能改balance的值吗
testrpc可以被query，但是独立的控制体系，在课程录制的时候没办法，现在有一些额外的工具可以去控制testrpc，这样的framework。
https://github.com/trufflesuite/ganache-cli
这个不是standard，只是为了测试方便

### 问 能讲下现在最新的智能合约发展趋势吗，就是其他区块链项目对solidity的改进，假如有时间的话
solidity有很大的问题：是一个不好formly define 形式化验证。现在的其他平台，比如definity，底层的架构是ewarsom，ewarsom在e0早期被作为虚拟机提出的，
ewarsom本身是基于web assembly
