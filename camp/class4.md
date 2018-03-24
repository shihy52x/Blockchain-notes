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
  - metacoin.getBalance.call('0x41041202a766ce36885314eed8624e9593aca3c1').then(result=>{console.log(result)})

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
 
