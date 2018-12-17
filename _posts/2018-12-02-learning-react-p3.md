---
layout: post
title:  "Self study javascript, ES6, React P3"
categories: javascript
author: tqtrung09
---

* content
{:toc}
Tiếp nối phần 2, phần này sẽ tìm hiểu nốt về các function của react. Sang phần sau thì chúng ta sẽ chuyển qua react-redux.





### `Conditional rendering`

Trong 1 function thì chỉ có duy nhất 1 hàm return, không tránh khỏi nhiều lúc chúng ta cần điều kiện khi render. Dưới đây là một số casestudy tiêu biểu.

```react
class ConditionalRendering extends React.Component {

  caseStudy1() {
    if(false) {
      return "false"
    }
    return "false"
  }

  caseStudy2() {
    let button ;
    if(true){
      button = <Button>submit</Button>
    } else {
      button = <Button>cancel</Button>
    }

    return (
      <div>
        {button}
      </div>
    )
  }

  caseStudy3() {
    // true && expression always evaluates to expression, and false && expression always evaluates to false.
    return (
      { true &&
        <p>
          true
        </p>
      }
    )
  }

  caseStudy4() {
    return (
      <div>
        {true ? (<Button>TRUE</Button>) : 'FALSE'}
      </div>
    )
  }
}
```

### `List and Keys`

In React, transforming arrays into lists of elements is nearly identical.

```react
function NumberList(props) {
  const numbers = [1, 2, 3, 4, 5];
  const listItems = numbers.map((number) =>
    <li>{number}</li>
  );
  return (
    <ul>{listItems}</ul>
  );
}
// Khi chạy code trên sẽ nhận được cảnh báo là cần thêm key cho list item

// Key được react sử dụng để xác định item nào change, item nào thêm mới hoặc remove.
const todoItems = todos.map((todo, index) =>
  // Only do this if items have no stable IDs
  <li key={index}>
    {todo.text}
  </li>
);

// Keys Must Only Be Unique Among Siblings
```

### `Form`
Khi 1 gía trị được nhập vào input form thì gía trị đó sẽ được lưu vào this.state.value thông qua hàm handleChange.

```react
class NameForm extends React.Component {
  constructor(props) {
    super(props);
    this.state = {
      value: '',
      value1: '',
      value2: ''
    };

    this.handleChange = this.handleChange.bind(this);
    this.handleSubmit = this.handleSubmit.bind(this);
  }

  handleChange(event) {
    // Với form có 1 input thì ta sử dụng cách setState này.
    this.setState({value: event.target.value});

    // Với form có nhiều input thì ta sử dụng cách setState này.
    // Ta cần thêm thuộc tính name cho input để phân biệt lúc setState.
    const target = event.target;
    const value = target.type === 'checkbox' ? target.checked : target.value;
    const name = target.name;

    this.setState({
      [name]: value
    });
  }

  handleSubmit(event) {
    alert('A name was submitted: ' + this.state);
    event.preventDefault();
  }

  render() {
    return (
      <form onSubmit={this.handleSubmit}>
        <input type="text" name="value" value={this.state.value} onChange={this.handleChange} />

        <textarea name="value1" value={this.state.value1} onChange={this.handleChange} />

        <select value={this.state.value2} name="value2" onChange={this.handleChange}>
          <option value="grapefruit">Grapefruit</option>
          <option value="lime">Lime</option>
          <option value="coconut">Coconut</option>
          <option value="mango">Mango</option>
        </select>

        <input
          name="isGoing"
          type="checkbox"
          checked={this.state.isGoing}
          onChange={this.handleChange} />

        <input type="submit" value="Submit" />
      </form>
    );
  }
}

```
Select tag đặt gía trị selected bằng cách gán gía trị initial trong constructor của component.


###`Composition vs Inheritance`
React rất mạnh với mô hình composition, và nó được recommend sử dụng để thay thế cho inheritance trong việc reuse code giữa các compoments.
Chúng ta sử dụng 1 thuộc tính đặc biệt `children` của props để truyền element con trực tiếp vào trong output.

```react
function FancyBorder(props) {
  return (
    <div className={'FancyBorder FancyBorder-' + props.color}>
      {props.children}
    </div>
  );
}

function WelcomeDialog() {
  return (
    <FancyBorder color="blue">
      <h1 className="Dialog-title">
        Welcome
      </h1>
      <p className="Dialog-message">
        Thank you for visiting our spacecraft!
      </p>
    </FancyBorder>
  );
}

```

1 case study khác.

```react
function SplitPane(props) {
  return (
    <div className="SplitPane">
      <div className="SplitPane-left">
        {props.left}
      </div>
      <div className="SplitPane-right">
        {props.right}
      </div>
    </div>
  );
}

function App() {
  return (
    <SplitPane
      left={
        <Contacts />
      }
      right={
        <Chat />
      } />
  );
}
```

