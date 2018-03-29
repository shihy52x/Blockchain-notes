# 前段程序的开发
truffle migrate --reset 将migration里面的步骤从新跑一遍，否则不会部署新修改的程序
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



onSelectAccount = (ev) => {
  this.setState({ selectAccount: ev.target.text})
}

//当前所点击的target，获取target里面的text，然后将这个text存在状态变量selectAccount里面，这个selectAccount后面要用
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
    const { payroll, web3 } = this.props; // props 是个dictionary，里面就是上面传入的 account, payrll(instance) and web3(instance).这里有两个，说明上面传错了
    const updateInfo = (error, result) => { //这个 updateInfo 的funciton合约里还没有，需要加上
      if (!error) {
        this.checkInfo();
      }
    }

    this.newFund = payroll.NewFund(updateInfo); //这个function需要手动写上
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



## Employer component

```
import React, { Component } from 'react'
import { Layout, Menu, Alert } from 'antd';

import Fund from './Fund';
import EmployeeList from './EmployeeList';

const { Content, Sider } = Layout;

class Employer extends Component {
  constructor(props) {
    super(props);

    this.state = {
      mode: 'fund'
    };
  }
//addFund是视频里面讲的，origin的source里面没有，同是还添加了addEmployee
addFund = () => {
  const { payroll, employer, web} = this.props;
  //传入payroll这个object来进行对contract的method和状态变量的使用，传入employer这个地址需要从这个地址转钱出去，传去web这个object需要用到web的一些method
 payroll.addFund({
    from: employer;
    gas: 10000 //这是你在调用方程的时候最多可以消耗的gas的量
    value: web3.toWei(this.fundInput.value)
    //页面输入的数值是以eth为单位的，需要转化成wei，this.fundInput.value 是员工的地址
  })
}
// 你也可以添加 function 来调用

addEmployee = () => {
const {employer , payroll} = this.props；
payroll.addEmployee(this.employeeInput.value,parseInt(this.SalaryInput.value),{from:employer, gas:10} ).then((result) => {alert('success')}) //异步的，所以调用之后，可以出来成功的提示;
}

//增加一个本地的function叫addFund,这个function的作用是调用从App.js传进来的参数，然后将相关参数的值通过payroll的method赋值给payroll里面的state varialbe注意，this 是指的整个 employer这个componenet这个object
// =》左边是相当于定义一个funciton的名字以及要带的参数，右边=> 是要定义的函数的内容

  componentDidMount() {
    const { account, payroll } = this.props;
    payroll.owner.call({
      from: account
    }).then((result) => {
      this.setState({
        owner: result
      });
    })
  }

  onSelectTab = ({key}) => {
    this.setState({
      mode: key
    });
  }

  renderContent = () => {
    const { account, payroll, web3 } = this.props;
    const { mode, owner } = this.state;

    if (owner !== account) {
      return <Alert message="你没有权限" type="error" showIcon />;
    }

    switch(mode) {
      case 'fund':
        return <Fund account={account} payroll={payroll} web3={web3} />
      case 'employees':
        return <EmployeeList account={account} payroll={payroll} web3={web3} />
    }
  }

  render() {
    return (
      <Layout style={{ padding: '24px 0', background: '#fff'}}>
        <Sider width={200} style={{ background: '#fff' }}>
          <Menu
            mode="inline"
            defaultSelectedKeys={['fund']}
            style={{ height: '100%' }}
            onSelect={this.onSelectTab}
          >
            <Menu.Item key="fund">合约信息</Menu.Item>
            <Menu.Item key="employees">雇员信息</Menu.Item>
          </Menu>
        </Sider>
        <Content style={{ padding: '0 24px', minHeight: 280 }}>
          {this.renderContent()}
        </Content>
      </Layout>
    );
  }
}

export default Employer
```
### employer supplement
```
<div>
  <h2>
  <form>
    <fieldset>
      <legend> Add fund </legend>
      <label> fund </label>
      <input type='text' placeholder='fund' ref= {(input) => {this.fundInput = input;}}> 
      // 定义了一个ref函数，参数是input，将input传入this.fundInput.为什么要多加个花括号呢？
      <button type="button" className = "pure-button" onClick ={this.addFund}>Add</Button>
```

### emloyee component
```
import React, { Component } from 'react'
import { Card, Col, Row, Layout, Alert, message, Button } from 'antd';

import Common from './Common';

class Employer extends Component {
  constructor(props) {
    super(props);
    this.state = {};
  }

  componentDidMount() {
    this.checkEmployee();
  }

  checkEmployee = () => {
  }

  getPaid = () => {
  }

  renderContent() {
    const { salary, lastPaidDate, balance } = this.state;

    if (!salary || salary === '0') {
      return   <Alert message="你不是员工" type="error" showIcon />;
    }

    return (
      <div>
        <Row gutter={16}>
          <Col span={8}>
            <Card title="薪水">{salary} Ether</Card>
          </Col>
          <Col span={8}>
            <Card title="上次支付">{lastPaidDate}</Card>
          </Col>
          <Col span={8}>
            <Card title="帐号金额">{balance} Ether</Card>
          </Col>
        </Row>

        <Button
          type="primary"
          icon="bank"
          onClick={this.getPaid}
        >
          获得酬劳
        </Button>
      </div>
    );
  }

  render() {
    const { account, payroll, web3 } = this.props;

    return (
      <Layout style={{ padding: '0 24px', background: '#fff' }}>
        <Common account={account} payroll={payroll} web3={web3} />
        <h2>个人信息</h2>
        {this.renderContent()}
      </Layout >
    );
  }
}

export default Employer

```

### employee supplement


```
checkEmployee = () =>{
  const {payroll,emloyee,web3} = this.props;
  payroll.employee.call(employee,{from: employee, gas:1000000
  }).then((result)=>{
        console.log(result)
        this.setState({
            salary:web3.fromwei(result[1].toNumber()), lastPaidData: new Date (result[2].toNumber()*1000)
            //新增加employee里面的的状态变量，因为之前没有，这个后面输出到屏幕上需要. Solidity里单位是秒，而js中是毫秒. Data这里毫秒转为时间
           
        })
    })
}

getPaid = () =>{
  const {payroll, employee, web3} = this.props;
  payroll.getPaid({from: employee, gas: 1000000}).then((result) = > {alert("You have got paid!")})
}

render(){
  const {salary,lastPaidDate} = this.state
  const {employee} = this.props;
  return(
    <div>
      <h2>员工 {employee} </h2>
      { !salary || salary == '0' ?
          <p> "you are not an employee" </p> :
          ( <div> 
              <p> Salary:{salary}</p>
              <p>LastPaidDate"{lastPaidData}</p>
              <button typ="button" className = "pure-button" onClick = {this.getPaid}>get paid here</button>
          )
      }
  
  )
}
```


### accounts component

```
import React, { Component } from 'react'
import { Table, Button, Modal, Form, InputNumber, Input, message, Popconfirm } from 'antd';

import EditableCell from './EditableCell';

const FormItem = Form.Item;

const columns = [{
  title: '地址',
  dataIndex: 'address',
  key: 'address',
}, {
  title: '薪水',
  dataIndex: 'salary',
  key: 'salary',
}, {
  title: '上次支付',
  dataIndex: 'lastPaidDay',
  key: 'lastPaidDay',
}, {
  title: '操作',
  dataIndex: '',
  key: 'action'
}];

class EmployeeList extends Component {
  constructor(props) {
    super(props);

    this.state = {
      loading: true,
      employees: [],
      showModal: false
    };

    columns[1].render = (text, record) => (
      <EditableCell
        value={text}
        onChange={ this.updateEmployee.bind(this, record.address) }
      />
    );

    columns[3].render = (text, record) => (
      <Popconfirm title="你确定删除吗?" onConfirm={() => this.removeEmployee(record.address)}>
        <a href="#">Delete</a>
      </Popconfirm>
    );
  }

  componentDidMount() {
    const { payroll, account, web3 } = this.props;
    payroll.checkInfo.call({
      from: account
    }).then((result) => {
      const employeeCount = result[2].toNumber();

      if (employeeCount === 0) {
        this.setState({loading: false});
        return;
      }

      this.loadEmployees(employeeCount);
    });

  }

  loadEmployees(employeeCount) {
  }

  addEmployee = () => {
  }

  updateEmployee = (address, salary) => {
  }

  removeEmployee = (employeeId) => {
  }

  renderModal() {
      return (
      <Modal
          title="增加员工"
          visible={this.state.showModal}
          onOk={this.addEmployee}
          onCancel={() => this.setState({showModal: false})}
      >
        <Form>
          <FormItem label="地址">
            <Input
              onChange={ev => this.setState({address: ev.target.value})}
            />
          </FormItem>

          <FormItem label="薪水">
            <InputNumber
              min={1}
              onChange={salary => this.setState({salary})}
            />
          </FormItem>
        </Form>
      </Modal>
    );

  }

  render() {
    const { loading, employees } = this.state;
    return (
      <div>
        <Button
          type="primary"
          onClick={() => this.setState({showModal: true})}
        >
          增加员工
        </Button>

        {this.renderModal()}

        <Table
          loading={loading}
          dataSource={employees}
          columns={columns}
        />
      </div>
    );
  }
}

export default EmployeeList

```

### accounts supplements
用来显示当前网络中的所有的账户
```
import React from react;
export default function Accounts({
//直接返回方程，这种组件在react里面叫stateless component
  accounts=[]
  onSelectAccount // 这两个是在app.js调用Accounts的时候传入的参数. onSelectAccount 是一个方程，会在点击时候触发
}){
  return (
    <div className "pure-menu sidebar">
      <span className = 'pure-menu-heading'>账户列表</span>
      <ul className = 'pure-menu-list'>
      { accounts.map(account=> (
        <li className = 'pure-menu-item' key =on {account} onClick = {onSelectAccount} > 
          <a href="#" className = "pure-menu-link">{account}</a>
         </li>
       }
  )
   
}

```

## 运行
执行 npm start
