# 前段程序的开发

员工组件
雇主组件
Accounts 组件
Comm组件
App.js

```
import React, { Component } from 'react'
import PayrollContract from '../build/contracts/Payroll.json'
import getWeb3 from './utils/getWeb3'

import { Layout, Menu, Spin, Alert } from 'antd';

import Employer from './components/Employer';
import Employee from './components/Employee';

import 'antd/dist/antd.css';
import './App.css';

const { Header, Content, Footer } = Layout;

class App extends Component {
  constructor(props) {
    super(props)

    this.state = {
      storageValue: 0,
      web3: null,
      mode: 'employer'
    }
  }

  componentWillMount() {
    // Get network provider and web3 instance.
    // See utils/getWeb3 for more info.

    getWeb3
    .then(results => {
      this.setState({
        web3: results.web3
      })
//getWeb3返回的值-是个 objec，赋给result，传给{}，然后将this这个object里面的web3状态变量设置为results.web3
      // Instantiate contract once web3 provided.
      this.instantiateContract()
    })
    .catch(() => {
      console.log('Error finding web3.')
    })
  }

  instantiateContract() {
    /*
     * SMART CONTRACT EXAMPLE
     *
     * Normally these functions would be called in the context of a
     * state management library, but for convenience I've placed them here.
     */

    const contract = require('truffle-contract')
    const Payroll = contract(PayrollContract)
    Payroll.setProvider(this.state.web3.currentProvider)

    // Declaring this for later so we can chain functions on Payroll.
    var PayrollInstance

    // Get accounts.
    this.state.web3.eth.getAccounts((error, accounts) => {
    //获取当前网络的所有账户
      this.setState({
        account: accounts[0],
      });
      //将app的状态账户设置weiaccounts[0]
      Payroll.deployed().then((instance) => {
        PayrollInstance = instance
        this.setState({
          payroll: instance
        });// 将contract部署之后，instance这个object赋给Payrollinstance和payroll，方便后面调用
      })
    })
  }

  onSelectTab = ({key}) => {
    this.setState({
      mode: key
    });//这是个account组件里面的function，随后会讲
  }

  renderContent = () => {
    const { account, payroll, web3, mode } = this.state;

    if (!payroll) {
      return <Spin tip="Loading..." />;
    }

    switch(mode) {
      case 'employer':
        return <Employer account={account} payroll={payroll} web3={web3} />
      case 'employee':
        return <Employee account={account} payroll={payroll} web3={web3} />
      default:
        return <Alert message="请选一个模式" type="info" showIcon />
    }
  }

  render() {
  //render中文一般翻译为渲染，是指将要显示在浏览器上的东西
    return (
      <Layout>
        <Header className="header">
          <div className="logo">老董区块链干货铺员工系统</div> 
          //div 是一种有蓝色背景的字框 https://www.w3schools.com/tags/tag_div.asp
          //<p> 是paragrah组件
          <Menu
            theme="dark"
            mode="horizontal"
            defaultSelectedKeys={['employer']}
            style={{ lineHeight: '64px' }}
            onSelect={this.onSelectTab}
          >
            <Menu.Item key="employer">雇主</Menu.Item>
            <Menu.Item key="employee">雇员</Menu.Item>
          </Menu>
        </Header>
        <Content style={{ padding: '0 50px' }}>
          <Layout style={{ padding: '24px 0', background: '#fff', minHeight: '600px' }}>
            {this.renderContent()}
          </Layout>
        </Content>
        <Footer style={{ textAlign: 'center' }}>
          Payroll ©2017 老董区块链干货铺
        </Footer>
      </Layout>
    );
  }
}

export default App
```
## supplement
```
<div classname="pure-u-1-3">
  <Accounts accounts={accounts} onSelectAccount={this.}>
  </div>
<div classname ="pure-u-2-3">
  {
  selectAccount === accounts[0]?
  <Employer employer = {selectAccount} payroll={payroll}>
    <Employee employee = {selectAccount} payroll = {payroll} web3= {web}>
}
  {payroll && <Common account = {selectAccount} payroll = {payroll} web3= {web > } // 这三个参数将传入common组件里的props
  </div>
// 这里div 组件后面跟的内容是输入自己穿件的组件，显示的内容就<1组件>显示内容<1组件>. 而花括号之间的内容会全部显示
export default App

```
Commen 组件

```
```



# common.js
```
import React, { Component } from 'react'
import { Card, Col, Row } from 'antd';

class Common extends Component {
  constructor(props) {
    super(props);

    this.state = {};
    //初始化一个state作为本文件的全局变量，赋值给dictionary
  }

  componentDidMount() {
  //当组件被加载到浏览器里的时候，将运行以下代码。 就是上面那个app.js里面最后的renderfunction 的<div> payroll && common call的
    const { payroll, web3 } = this.props; // props 是个dictionary，里面就是上面传入的 account, payrll(instance) and web3(instance)
    const updateInfo = (error, result) => {
      if (!error) {
        this.checkInfo();
      }
    }

    this.newFund = payroll.NewFund(updateInfo);
    this.getPaid = payroll.GetPaid(updateInfo);
    this.newEmployee = payroll.NewEmployee(updateInfo);
    this.updateEmployee = payroll.UpdateEmployee(updateInfo);
    this.removeEmployee = payroll.RemoveEmployee(updateInfo);

    this.checkInfo();
  }

  componentWillUnmount() {
    this.newFund.stopWatching();
    this.getPaid.stopWatching();
    this.newEmployee.stopWatching();
    this.updateEmployee.stopWatching();
    this.removeEmployee.stopWatching();
  }

  checkInfo = () => {
    const { payroll, account, web3 } = this.props;
    payroll.checkInfo.call({
      from: account,
    }).then((result) => {
      this.setState({
        balance: web3.fromWei(result[0].toNumber()),
        runway: result[1].toNumber(),
        employeeCount: result[2].toNumber()
      })
    });
  }

  render() {
    const { runway, balance, employeeCount } = this.state;
    return (
      <div>
        <h2>通用信息</h2>
        <Row gutter={16}>
          <Col span={8}>
            <Card title="合约金额">{balance} Ether</Card>
          </Col>
          <Col span={8}>
            <Card title="员工人数">{employeeCount}</Card>
          </Col>
          <Col span={8}>
            <Card title="可支付次数">{runway}</Card>
          </Col>
        </Row>
      </div>
    );
  }
}

export default Common
```
