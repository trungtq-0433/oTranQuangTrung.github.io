---
layout: post
title:  "Self study javascript, ES6, React P4"
categories: javascript
author: tqtrung09
---

* content
{:toc}
Phần này chúng ta sẽ phân tích các thành phần của redux.







### `Install`
[React-Redux](https://github.com/reduxjs/react-redux) is the official React binding for Redux.
React-Redux version 6 requires React 16.4 or later.
To use React-Redux with your React app:
```
npm install --save redux
npm install --save react-redux
```
### `Overview`
![](https://cloud.netlifyusercontent.com/assets/344dbf88-fdf9-42bb-adb4-46f01eedd629/44e53712-498d-400f-8967-8ed0dbdb90a1/new-redux-data-flow-large-opt.png)
Để hiểu về Data flow trên ta xem ví dụ dưới đây trước khi đi vào các khái niệm cụ thể.

```react
import { createStore } from 'redux';

// Khởi tạo state
var initialState = {
    status : false
}

// REDUCER
var myReducer = (state = initialState, action) => {
  if (action.type === 'TOGGLE_STATUS') {
    let newState = {...state}
    newState.status = !state.status;
    return newState; // Trả về state mới để lưu vào store.
  }

  return state;
}

// STORE
const store = createStore(myReducer); // Khởi tạo store.

console.log('Default:', store.getState());

// ACTION
var action = { type : 'TOGGLE_STATUS'};

store.dispatch(action); // Gửi action tới reducer để thực hiện action

// VIEW
console.log('TOGGLE_STATUS', store.getState());
```
![](https://images.viblo.asia/2762ed06-128a-4c95-8d69-683ef3b1970f.png)


Có 4 thành phần chính chúng ta cần tìm hiểu và cần nhớ trong lifecycle của redux: Action, Reducer, Store, View.

### `Store`
Store Là nơi quản lý state, có thể truy cập để lấy state ra, update state hiện có, và listener để nhận biết xem có thay đổi gì không, và cập nhật qua views.

```react
import { Provider } from "react-redux";
import { createStore } from 'redux';
import TrackList from './components/TrackList';
import * as actions from './actions';
import rootReducer from '../reducers/index';

const rootElement = document.getElementById("root");
const store = createStore(rootReducer);
const tracks = [
  {
    id: 1,
    title: 'Em cua ngay hom qua'
  }
];
store.dispatch(actions.setTracks(tracks));

ReactDOM.render(
  <Provider store={store}>
    <TrackList />
  </Provider>,
  rootElement
);
```

Redux cung cấp cho chúng ta:
`createStore` để khởi tạo store.

`<Provider />` được sử dụng để gói tất cả component vào trong này.
Mục đích là để cho tất cả function của các component con đều có thể truy cập store, store được truyền vào như 1 property.

### `Action`

Là nơi mang các thông tin dùng để gửi từ ứng dụng đến Store. Action là những đối tượng mô tả cách chúng ta muốn thay đổi state. Bạn có thể hình dung action như là những API cho state của bạn. Theo đó, action bản chất không gọi bất cứ thứ gì. Một action chỉ đơn giản là một object mô tả về cách chúng ta muốn thay đổi state.

1) Action Type:
  Chúng ta cần 1 file để chưa constant, các constant này sử dụng để giải quyết việc xác định sử dụng action nào. Action type được sử dụng ở cả action và reducer.

```react
// src/core/constants.js

export const ActionTypes = {
  TRACKS_SET: 'TRACKS_SET'
}
```

2) Action Creators:
  Action creator sẽ trả về một object với type và payload. Type là một trong số những constant ActionTypes mà chúng ta đã tạo ở trên. Payload có thể là bất cứ thứ gì, nó sẽ được sử dụng để thay đổi global state của application.

```react
// src/actions/tracks.js

import {ActionTypes} from '../core/constants';

export function setTracks(tracks) {
  return {
    type: ActionTypes.TRACKS_SET,
    tracks // Tương đương với tracks: tracks
  };
};
```
Action creator sẽ nhận vào input là list tracks, cái mà chúng ta sẽ set vào global state. Action creator này sẽ trả về một object chứa action type và payload là list mà chúng ta đã truyền vào.

Để giữ cho folder structure gọn gàng sau này có nhiều action, chúng ta sẽ tạo một file index.js trong folder actions để chứa tất cả các action creator, sau đó chỉ cần import 1 file index.

```react
import {setTracks} from './track';

export {
  setTracks
};

```


### `Reducer`
Action tự bản thân nó không thể làm nên được điều gì ngoài việc định nghĩa. Reducer là những action handler, nó hoạt động kết nối giữa action và store và biến thành những thay đổi trong state.Nếu chúng ta dispatch một action là setTracks vào trong store, chúng ta có thể có một reducer nhận action đó và update tracks mới vào trong state.
Nói một cách đơn giản nó nhận vào type và payload action trả về, và từ đó biến state cũ thành state mới.
Lưu ý rằng thay vì thay đổi state trước đó thì chúng ta sẽ trả về một object state mới. State trong redux phải là immutable.

```react
import {ActionTypes} from '../core/constants';

const initialState = [];

export default function(state = initialState, action) {
  switch (action.type) {
    case ActionTypes.TRACKS_SET:
      return setTracks(state, action);
  }

  return state;
}

function setTracks(state, action) {
  const {tracks} = action;
  return [...state, ...tracks];
}
```
Cũng giống như cách chúng ta làm với action creator, chúng ta sẽ tạo một file index.js trong folder reducers để chứa hết tất cả reducer, và cung cấp ra cho các thành phần khác của application sử dụng.

```react
import {combineReducers} from 'redux';
import track from './track';

export default combineReducers({
  track
});
```

Khi sử dụng combineReducers, bạn có thể sử dụng multiple reducer mỗi reducer sẽ trả về một substate.

```react
// Nếu không sử dụng nó bạn sẽ phải access list tracks trong global state như thế này.
state.tracks

// Nhưng với combineReducers, bạn sẽ get thẳng từ subset của state như sau.
state.track.tracks

```



Tiếp theo là các method được redux cung cấp để hỗ trợ việc implement.

### `mapStateToProps`
**Không phải component nào cũng cần toàn bộ global store, chúng ta cần chỉ định state nào cần lấy cho mỗi component.**
Hàm này (mapState for short) được thực thi mỗi khi store state có thay đổi, để trả về substate của global state, cái mà component cần.

```react
function mapStateToProps(state, ownProps) {
  return {xxx: state.xxx}
}
```
`state` là global store state.

`ownProps` là tham số optional. Đây là props được truyền vào từ component cha.

Hàm này phải trả về 1 object và là tham số đầu tiên của `connect()` function.

### `mapDispatchToProps`
Hàm này (mapDispatch for short) là tham số thứ 2 được truyền vào `connect()` function với nhiệm vụ map hàm dispatch() của store trở thành 1 thuộc tính của props của component đó.

```react
const increment = () => ({ type: 'INCREMENT' })
const decrement = () => ({ type: 'DECREMENT' })
const reset = () => ({ type: 'RESET' })

const mapDispatchToProps = dispatch => {
  return {
    // dispatching actions returned by action creators
    increment: () => dispatch(increment()),
    decrement: () => dispatch(decrement()),
    reset: () => dispatch(reset()),

    onClick: event => dispatch(trackClick(event))
  }
}
// Sau khi maps vào props của component thì chúng ta có thể gọi trực tiếp các dispatch để thực thi action.
```

### `connect()`
**Connect function được sử dụng để connect React component tới Redux store.**

```react
function connect(mapStateToProps?, mapDispatchToProps?, mergeProps?, options?)
```

Connect chấp nhận 4 parameters khác nhau, tất cả đều là optional.
- mapStateToProps?: Function
- mapDispatchToProps?: Function or Object
- mergeProps?: Function
- options?: Object

```react
export default connect(mapStateToProps, mapDispatchToProps)(TrackList)
```

helper function `connect()` nhận tham số là 1 function làm nhiệm vụ mapping global state vào props của component. kết quả trả về của function `connect` là một function (kiểu hiểu nôm na là hàm `connect` sẽ trả về 1 function), function đó lại nhận vào 1 tham số khác là `TrackList`. Sau đó đem tất cả mọi thứ trả về cho component cha, component cha sẽ có thể truy xuất store, trong khi TrackList sẽ chỉ là component hiển thị dữ liệu.
