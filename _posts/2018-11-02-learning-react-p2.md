---
layout: post
title:  "Self study javascript, ES6, React P2"
categories: javascript
author: tqtrung09
---

* content
{:toc}
Tiếp nối phần 1 về ES6, phần này chúng ta sẽ tìm hiểu về ReactJS.






### `Setup`
Chúng ta cần chuẩn bị NodeJS và NPM, sau đó install package `create-react-app`
`npm install -g create-react-app`
Tiếp theo ta sử dụng package này để new react app
`npx create-react-app react-demo`

### `JSX`
React sử dụng JSX thay thế cho Javascript đơn thuần, mặc dù không phải là bắt buộc tuy nhiên nó được khuyên dùng.
Cá nhân mình thấy đơn thuần nó cũng chỉ là cú pháp thế nên phần này vừa làm vừa học là hợp lý nhất :v
Hoặc là đọc qua để biết một số cú pháp cơ bản.
> https://reactjs.org/docs/introducing-jsx.html

### `Component & Props`
Chúng ta sử dụng ES6 class để define component.

```js
// Welcome.js
class Welcome extends React.Component {
  constructor(props) {
    super(props);
  }

  render() {
    return <h1>Hello, {this.props.name}</h1>;
  }
}

// Khai báo defaul props, trường hợp không truyền data vào thì nhận gía trị mặc định.
Welcome.defaultProps = {
   name: "xxx"
}

// Validatetion Props
Welcome.propTypes = {
  name: React.PropTypes.string,

  // Các type mẫu
  propArray: React.PropTypes.array.isRequired,
  propBool: React.PropTypes.bool.isRequired,
  propFunc: React.PropTypes.func,
  propNumber: React.PropTypes.number,
  propObject: React.PropTypes.object
}

// Export component để import chỗ khác.
export default Welcome;
```

```js
// Main.js
import React from 'react';
import ReactDOM from 'react-dom';
import Welcome from './Welcome.js';

// Truyền gía trị props vào cho Welcome component
ReactDOM.render(<Welcome name="yyy" />, document.getElementById('root'));
```

Khi định nghĩa 1 component thì chúng ta cần nhớ props là read-only. Với lưu ý này chúng ta có khái niệm pure.
Pure có nghĩa là không thay đổi gía trị input.

```javascript
// Pure
function sum(a, b) {
  return a + b;
}
// Impure
function withdraw(account, amount) {
  account.total -= amount;
}
```
Tất cả react component cần phải hoạt động giống như pure function, không được thay đổi props.

### `State & Lifecycle`
Khác với props là dữ liệu được truyền từ ngoài vào component thì state là một biến được tạo ra trong component sử dụng để lưu trữ, thao tác dữ liệu trong một component.

```js
class Welcome extends React.Component {
  constructor(props) {
    super(props);
    // Khởi tạo state
    this.state = {
      name: "xxx"
    }
  }

  render() {
    return <h1>Hello, {this.state.name}</h1>;
  }
}
```

Để cập nhật state cho component, chúng ta sử dụng setState() method.
Ngoài ra còn có forceUpdate(), tuy nhiên method này được khuyến cáo là hạn chế sử dụng, vì nó có thể dẫn tới vòng lặp vô hạn.

```js
// Wrong
this.state.name = 'zzz';

// Correct
this.setState({name: 'zzz'});
```
setState() không cập nhật tức thì component ngay sau khi gọi. Vì vậy nếu chúng ta sử dụng this.state ngay sau khi gọi setState() thì sẽ không nhận được gía trị mới update.
Thay vào đó chung ta sửu dụng componentDidUpdate() để lấy gía trị sau khi cập nhật, hoặc sử dụng callback setState(updater, callback)

**Lifecycle**

```js
class Lifecycle extends React.Component {
  constructor(props) {
    /*
    Đây là hàm khởi tạo của component, thường được sử dụng với 2 mục đích chính đó là khởi tạo state, và binding event handle.
    this.state = { name: "xxx" };
    this.handleClick = this.handleClick.bind(this);

    Không sử dụng setState() trong constructor.
    Không copy props vào state, vì chúng ta có thể sử dụng luôn props, và có thể xảy ra lỗi là cập nhật props nhưng không phản ánh được trong state.
    this.state = { name: props.name };
    */
  }

  componentDidMount() {
    /*
    Hàm này được gọi ngay tức thì khi component được mount.
    Request api thường được định nghĩa ở đây.
    Có thể sử dụng setState() trong hàm này tuy nhiên việc sử dụng như vậy thì làm cho hàm render() bị gọi 2 lần làm ảnh hưởng đến hiệu suất. Thế nên ta sẽ khởi tạo gía trị state ngay trong hàm constructor.
    */
  }

  componentDidUpdate(prevProps, prevState) {
    /*
    Hàm này được gọi khi có sự kiện update component, còn trong trường hợp khởi tạo thì không được gọi.
    Đây cũng là nơi tốt để thực hiện request api, tuy nhiên cần lưu ý là cần so sánh gía trị props (trong điều kiện là request api làm thay đổi props)
    if (this.props.userID !== prevProps.userID) {
      this.fetchData(this.props.userID);
    }
    Trong này cũng có thể sử dụng setState() tuy nhiên, cũng như trên chúng ta cần phải kiểm tra điều kiện của state. Bởi vì, hàm setState() là hàm sẽ tác động cập nhật component, mà component cập nhật thì sẽ gọi hàm componentDidUpdate() thế nên sẽ tạo nên vòng lặp vô hạn.
    Hàm này sẽ không được gọi nếu shouldComponentUpdate() returns false.
    */
  }

  componentWillUnmount() {
    /*
    Hàm được gọi trước khi 1 component unmounted, remove khỏi DOM.
    Hàm được sử dụng để xử lý các resource của component trước khi nó được unmounted.
    Không sử dụng setState() ở trong này vì component này sẽ không được render lại. 1 component sau khi unmounted sẽ không thể mounted lại 1 lần nào nữa.
    */
  }

  render() {
    /*
    Là method duy nhất được yêu cầu bắt buộc có trong 1 component.
    Hàm này sẽ không được gọi nếu shouldComponentUpdate() returns false.
    */
  }
}
```

**Rarely Used Lifecycle Methods**

Đây là  một số method cũng nằm trong lifecycle tuy nhiên rất hiếm khi sử dụng đến. Thế nên mình chỉ để cập qua, lúc nào cần sử dụng đến thì search để tìm hiểu kỹ lại.
https://reactjs.org/docs/react-component.html#rarely-used-lifecycle-methods

```js
shouldComponentUpdate(nextProps, nextState) {
  /*
  Hàm chỉ được gọi khi có sự update props hoặc state, mặc định hàm sẽ trả về true, có nghĩa là render lại view, trả về false là sẽ ko render lại view.
  Phương thức này sẽ không được gọi khi render khởi tạo lần đầu.
  Nếu return false thì các method componentWillUpdate(), componentDidUpdate(), render() sẽ không được gọi.
  */
}

static getDerivedStateFromProps(props, state){
  // Hàm được gọi trước khi gọi render() method, cả case init và update.
}

getSnapshotBeforeUpdate(prevProps, prevState) {}
```

**Legacy Lifecycle Methods**
Các method được đề cập dưới đây đều được gán mark là "legacy". Các method này vẫn hoạt động tuy nhiên không được recommend sử dụng.

```js
UNSAFE_componentWillMount() {
  /*
  Hàm được thực hiện 1 lần duy nhất, trước khi thực hiện lần khởi tạo render() đầu tiên, nó sẽ chạy trước khi chạy tới hàm render().
  Tại đây không được sử dụng setState() mà chuyển vào trong constructor.
  Method này không được khuyến khích dùng mà thay vào đó là sử dụng componentDidMount().
  Method này nguyên bản có tên là componentWillMount() sẽ được sử dụng tới bản react v17 sau đó sẽ được đổi tên thành UNSAFE_componentWillMount()
  */
}

UNSAFE_componentWillReceiveProps(nextProps) {
  /*
  Hàm được gọi liên tục khi 1 component nhận 1 gía trị mới. Thông qua updateParent().
  setState() sẽ không gọi đến hàm này.
  Hàm này cũng được khuyến cáo hạn chế sử dụng, thay vào đó thì ta sử dụng componentDidUpdate() để thay thế.
  Tương tự, ở v17 thì hàm sẽ được đổi tên từ componentWillReceiveProps() => UNSAFE_componentWillReceiveProps()

  */
}

UNSAFE_componentWillUpdate(nextProps, nextState) {
  /*
  Hàm này được gọi khi props hoặc state nhận gía trị mới. Ví dụ sau khi qua hàm setState() thì hàm này sẽ được gọi.
  Trong này không được sử dụng setState(), bởi vì khi gọi setState() sẽ chạy lại vào hàm này, dẫn tới vòng lặp vô hạn.
  Tương tự, ở v17 thì hàm sẽ được đổi tên từ componentWillUpdate() => UNSAFE_componentWillUpdate().
  Hàm này cũng được khuyến cáo hạn chế sử dụng, thay vào đó thì ta sử dụng componentDidUpdate() để thay thế.
  Hàm này sẽ không được chạy nếu componentShouldUpdate() return false
  */
}
```

### `Handling events`

React events đặt tên theo quy tắc camelCase.

```js
class HandlingEvent extends React.Component {
  constructor(props) {
    super(props);
    this.state = {isToggleOn: true};

    // Binding this vào hàm
    this.handleClick = this.handleClick.bind(this);
  }

  handleClick() {
    this.setState(state => ({
      isToggleOn: !state.isToggleOn
    }));
  }

  render() {
    return (
      <button onClick={this.handleClick}>
        {this.state.isToggleOn ? 'ON' : 'OFF'}
      </button>
    );
  }
}

// Nếu sử dụng arrow function thì không cần bind this.

class HandlingEvent extends React.Component {
  // This syntax ensures `this` is bound within handleClick.
  // Warning: this is *experimental* syntax.
  handleClick = () => {
    console.log('this is:', this);
  }

  render() {
    return (
      <button onClick={this.handleClick}>
        Click me
      </button>
    );
  }
}

// Truyền biến qua handle event.

<button onClick={(e) => this.deleteRow(id, e)}>Delete Row</button>
<button onClick={this.deleteRow.bind(this, id)}>Delete Row</button>

```











