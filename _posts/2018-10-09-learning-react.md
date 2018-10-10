---
layout: post
title:  "Self study javascript, ES6, React"
categories: javascript
author: tqtrung09
---

* content
{:toc}
Mình là backend-developer, tuy nhiên dạo gần đây mình có tập trung nhiều hơn mảng frontend.
Trước làm tay ngang kiểu học mót thì cũng biết được gọi là chút css, javascript, jquery, angularjs các kiểu, tuy nhiên cũng chưa có lần nào học bài bản và  ghi chép lại. Nay mình bắt đầu học về React thì mình cũng có ghi chép lại một phần là để sau này lúc làm có cái lục lại một phần là hy vọng tài liệu của mình có thể giúp ích được một ai đó bắt đầu học từ đầu như mình.






Như mình đã nói ở trên, thì các bạn cũng cần phải có một chút nền tảng về javascript để bắt đầu. Mà không có cũng chẳng sao, không biết đâu thì tìm hiểu học ở đó rồi học tiếp.

Đầu tiên sẽ là ECMAScript-6, mình sẽ tìm hiểu những tính năng cơ bản trên bản ES6 này.

### `Const, let and block-scope`
const và let được sử dụng để khai báo các biến, tuy nhiên điểm khác nhau cần nhớ là:
- const được sử dụng để khai báo các biến mà tính chất là hằng số, không thể định nghĩa lại.
- let được sử dụng để khai báo các biết mà có thể ghi đè hay gán lại.
- block-scope là phạm vi trong khối được xác định bằng cặp dấu {}.

Rõ hơn thì chúng ta xem ví dụ sau.

```js
const XXX = "xxx"
XXX = "yyy" 
// Uncaught TypeError: Assignment to constant variable.

let XXX = "xxx"
XXX = "yyy" => OK

if(true) {
  let x = "hello";
  {
    let x = "world";
    console.log("Value 1, x = " + x);
  }
  console.log("Value 2, x = " + x);
}
console.log("Value 3, x = " + x);

// prints
Value 1, x = world
Value 2, x = hello
Value 3 => undefined

```

### `Arrow function` 
Đây là cú pháp mới cho việc định nghĩa function, chúng ta hãy cùng xem các ví dụ sau đây.

```js
// Trước đây chúng ta định nghĩa function như sau.
function xxx(arg1, arg2){

}
hoặc
var xxx = function(arg1, arg2){
}

// Đây là cú pháp của arrow function
var xxx = (arg1, arg2) => {// có nhiều biến tham số đầu vào
}

// Đây là cú pháp của 1 tham số đầu vào
var xxx = arg => {
}

// Đây là ví dụ không có tham số truyền vào
var xxx = () => {
}

// Sử dụng với map, forEach
array.map(v => v + 1)
array.map(v => ({ x: v, y: v + 1 }))
array.map((v, i) => v + i)

array.forEach(v => {
  if (v % 5 === 0) {fives.push(v)}
})
```

Ở trước ES6 thì mỗi hàm khai báo đều có 1 giá trị `this`tách biêt, ta cùng xem ví dụ sau:

```js
'use strict'
function App() {
  this.count = 0
  setInterval(function() {
    console.log(this.count++) // Lỗi
  }, 1000)
}

var a = new App()
====
function App() {
  this.count = 0
  var self = this // Cần gán reference 
  setInterval(function() {
    console.log(self.count++)
  }, 1000)
}
```

Với arrow function thì chúng ta không cần dùng biến tạm hay `.bind` nữa mà giá trị this chính là this trong tầm gần nhất với chỗ gọi. (lexical this)

```js
function App() {
  this.count = 0

  setInterval(() => console.log(this.count++), 1000)
}
```

### `Destructuring Assignments`
Đây là kỹ thuật tách các phần tử của array hoặc object thành nhiều biến. Chúng ta cùng xem các ví dụ với array và object sau để hình dung được kỹ thuật này.

```js
let [a, b, c] = [1, 2, 3]
=> a = 1
=> b = 2
=> c = 3

let [, , c] = [1, 2, 3]
=> c = 3

let xxx = {
  a: 1,
  b: 2,
  c: 3,
  d: {
    e: 4
  }
}

let {aa: a, bb: b, cc: c} = xxx
=> aa = 1
=> bb = 2
=> cc = 3

let {a} = xxx
=> a = 1

let {d: {ee: e}} = xxx
=> ee = 4
```

### `Default Parameters`
Đây là cú pháp gán giá trị mặc định cho tham số truyền vào. Chúng ta xem các ví dụ sau:

```js
var xxx = x => {
  x = x || 2 // Trước ES6 thì sử dụng thế này
  return x;
}

var xxx = (x = 2) => { // ES6 có thể làm thế này
  return x;
}
```

Việc khai báo giá trị mặc định ngay đầu vào của ES6 này làm chúng ta dễ bao quát tổng quan 1 function hơn.

### `Rest parameter and Spread operator`
Rest là kỹ thuật khai báo hàm với tham số không xác định, ngoài các tham số xác định, chúng ta có thể khai bảo các tham số còn lại của function bằng các sử dụng cú pháp `...`

```js
let xxx = (a, b, ...other) => {
}
xxx(1, 2, 3, 4)
=> a = 1
=> b = 2
=> other = [3, 4]
```

Spread là kỹ thuật kết hợp một mảng đã có sẵn thành mảng mới.

```js
let xxx = [1, 2, 3, 4]
let yyy = [...xxx, 5, 6, 7]
=> yyy = [1, 2, 3, 4, 5, 6, 7]
```

### `Template Literals`
Ở trước ES6 thì chúng ta sử dụng biểu thức cộng để nối string hay đưa biến vào trong string. Hãy xem với kỹ thuật này ES6 làm được những gì nhé.

```js
let name = "xxx"
let hello = `hello ${name}`

const xxx = (arg1, arg2) => `${arg1} ${arg2}`
const yyy = `hello ${xxx("Trung", "Tran")}`

let xxx = `hello XXX
next line
`
```

### `Object properties`
ES6 cung cấp cú pháp đơn giản trong việc khởi tạo. 

```js
let x = 1, y = 2
let xy = {x, y}
```

### `Import and Export`
2 kỹ thuật này được sử dụng để tách các thành phần chung, tái sử dụng resource.

```js
//  lib/math.js
export function sum (x, y) { return x + y }
export var pi = 3.141593

//  someApp.js
import * as math from "lib/math"
console.log("2π = " + math.sum(math.pi, math.pi))

//  otherApp.js
import { sum, pi } from "lib/math"
console.log("2π = " + sum(pi, pi))
```

### `Classes`
Trong lập trình OOP chúng ta đã quen với class. Class trong ES6 có những tính chất gì và được sử dụng như thế nào, chúng ta cùng xem các ví dụ sau đây.

```js
// Define
class Xxx {
  constructor(x, y) {
    this.x = x
    this.y = y
  }
}

let xxx = new Xxx(1, 2)

// Inheritance
// Muốn kế thừa class nào đó chúng ta sử dụng từ khóa extends
class Yyy extends Xxx {
  constructor(x, y) {
    super(x, y)
  }
}

// Static class member
class Xxx {
  static yyy() {
    return `yyy`
  }
}

=> console.log(Xxx.yyy())

// Getter and Setter
// Được sử dụng với mục đích giới hạn quyền đọc ghi đối với class.
class Circle {
  constructor(radius) {
    this._radius = radius
  }
  get area() { return Math.PI * this._radius * this._radius }
  set radius(r) { this._radius = r }
}
var c = new Circle(4)
// returns: Circle {_radius: 4}
c.area
// returns: 50.26548245743669
c.radius = 6
c.area
// returns: 113.09733552923255

```

### `Iterator`

```js
let arr = [2,3,4,1];
for (let value of arr) {
   console.log(value);
}

let string = "Javascript";
for (let char of string) {
   console.log(char);
}
```

### `Map and Set data structure`
Array và Object là 2 kiểu cấu trúc dữ liệu chúng ta thường xuyên sử dụng nhất. Tuy nhiên ES6 cung cấp thêm 2 kiểu nữa để linh hoạt sử dụng trong từng trường hợp khác nhau.

Map là kiểu cấu trúc key-value, tương tự như Array tuy nhiên chúng ta có thể định nghĩa thêm index, và chúng ta có thể sử dụng bất kỳ giá trị nào làm index, index này là duy nhất trong Map.

```js
var map = new Map();
map.set('name', 'John');
map.set('name', 'Andy');
map.set(1, 'number one');
map.set(NaN, 'No value');
map.get('name'); // Andy. Note John is replaced by Andy.
map.get(1); // number one
map.get(NaN); // No value

// Thêm 1 ví dụ khác
var map = new Map();
map.set('name', 'John');
map.set('id', 10);
map.size; // 2. Returns the size of the map.
map.keys(); // outputs only the keys. 
map.values(); // outputs only the values.
for (let key of map.keys()) {
   console.log(key);
}
Output:
name
id

for (let element of map) {
 console.log(element);
}
Output;
['name', 'John']
['id', 10]

for (let [key, value] of map) {
  console.log(key+" - "+value);
}
Output:
name - John
id - 10
```

Set được sử dụng để lưu trữ dữ liệu với đặc điểm là value là duy nhất. Chúng ta cùng xem ví dụ sau:

```js
var sets = new Set();
sets.add('a').add('b').add('a');
// We are adding duplicate value.
sets.has('a') === true
sets.size === 2
for (let element of sets) {
  console.log(element);
}
Output:
a
b
```

### `New built-in method`
ES6 cung cấp một số method để chúng ta sử dụng thuận tiện hơn và dễ dàng hơn.

```js
// Object assign
let dest = { quux: 0 }
let src1 = { foo: 1, bar: 2 }
let src2 = { foo: 3, baz: 4 }
Object.assign(dest, src1, src2)
dest.quux === 0
dest.foo  === 3
dest.bar  === 2
dest.baz  === 4

// Array finding
// Trả về phần tử đầu tiên thỏa mãn điều kiện.
[ 1, 3, 4, 2 ].find(x => x > 3) // 4
// Trả về index của phần tử đầu tiên thỏa mãn điều kiện.
[ 1, 3, 4, 2 ].findIndex(x => x > 3) // 2

// String searching
"hello".startsWith("h") // true
"hello".endsWith("lo")   // true
"hello".includes("ell")       // true
"hello".includes("ell", 1)    // true
"hello".includes("ell", 2)    // false

// Number Type Checking
Number.isNaN(42) === false
Number.isNaN(NaN) === true

Number.isFinite(Infinity) === false
Number.isFinite(-Infinity) === false
Number.isFinite(NaN) === false
Number.isFinite(123) === true
```

Tạm thời phần đầu về ES6, phần tiếp theo về React mình sẽ cập nhật sau.















































