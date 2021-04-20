# my

1. 如下表达式会输出什么？

```js
5 * '5'
16 + '5'
16 + ''
16 > '5'
'5' > '5'
```

<details>
<summary>答案</summary>

```js
5 * '5' // 25
16 + '5' // '165'
16 + '' // '16'
16 > '5' // true
'5' > '5' // false
```

* JavaScript 遇到预期为数值的地方，就会将参数值自动转换为数值。系统内部会自动调用Number()函数。除了加法运算符（+）有可能把运算子转为字符串，其他运算符都会把运算子自动转成数值。
* 加法运算符，如果一个运算子是字符串，另一个运算子是非字符串，这时非字符串会转成字符串，再连接在一起。
* 比较运算符，如果两个运算子都是原始类型的值，则是先转成数值再比较。而都是字符串则按照字典顺序进行比较。

</details>
<br><br>

2. 如下执⾏后会输出什么?

```js
var obj = {
    a: 10,
    b: () => {
        console.log(this.a);
        console.log(this);
    }
}
obj.b()
```

<details>
<summary>答案</summary>

```js
var obj = {
    a: 10,
    b: () => {
        console.log(this.a); // undefined
        console.log(this); // window
    }
}
obj.b()
```

* 箭头函数没有自己的 this，它的 this 就是外层代码块的 this，此处的外层就直接到了全局作用域，所以指向了 window
</details>
<br><br>

3. 如下执⾏后会输出什么?

```js
let department = '58-ABG-FE';
console.log('my department is ' + (department === '58-ABG-FE') ? '58-ABG-FE' : '58-ABG-RD')
```

<details>
<summary>答案</summary>

```js

let department = '58-ABG-FE';
console.log('my department is ' + (department === '58-ABG-FE') ? '58-ABG-FE' : '58-ABG-RD')
// '58-ABG-FE'
```

* 加减乘除，还有条件运算符，与或非，还有自增自减运算符的优先级都比条件运算符（?:）的优先级高，所以先计算 `'my department is ' + (department === '58-ABG-FE')` 然后才会计算条件运算符的结果
</details>
<br><br>

4. 如下输出什么？

```js
const user = {
    name: 'fe',
    age: 28,
    company: {
        department: 'ABG-FE'
    }
};
const { company: {department = 'ABG-RD'}} = user;
console.log(department);
```

<details>
<summary>答案</summary>

```js
const user = {
    name: 'fe',
    age: 28,
    company: {
        department: 'ABG-FE'
    }
};
const { company: {department = 'ABG-RD'}} = user;
console.log(department); // 'ABG-FE'
```
</details>
<br><br>

5. 如下输出什么？

```js
const print = (fn) => {
    let a = 200;
    fn();
}
let a = 100;
const fn = () => {
    console.log(a);
};
print(fn);

```

<details>
<summary>答案</summary>

```js
const print = (fn) => {
    let a = 200;
    fn();
}
let a = 100;
const fn = () => {
    console.log(a);
};
print(fn); // 100

```
* 函数 fn 是在函数 print 的外部声明的，所以它的作用域绑定外层，内部变量 a 不会到函数 print 体内取值，所以输出 100，而不是 200。
* 函数执行时所在的作用域，是定义时的作用域，而不是调用时所在的作用域。
</details>
<br><br>

6. 什么是闭包？⼿写⼀个闭包?

<details>
<summary>答案</summary>

```js
function f1() {
  var n = 999;
  function f2() {
    console.log(n);
  }
  return f2;
}

var result = f1();
result(); // 999
```

闭包就是函数 f2，即能够读取其他函数内部变量的函数。由于在 JavaScript 语言中，只有函数内部的子函数才能读取内部变量，因此可以把闭包简单理解成“定义在一个函数内部的函数”。闭包最大的特点，就是它可以“记住”诞生的环境，比如f2记住了它诞生的环境 f1，所以从 f2 可以得到 f1 的内部变量。在本质上，闭包就是将函数内部和函数外部连接起来的一座桥梁。
</details>
<br><br>

7. 如下输出什么？

```js
function foo() {
    return a => {
        console.log(this.a);
    }
}
const obj1 = {a: 2};
const obj2 = {a: 3};
const bar = foo.call(obj1);
console.log(bar.call(obj2))
```
<details>
<summary>答案</summary>

```js
function foo() {
    return a => {
        console.log(this.a);
    }
}
const obj1 = {a: 2};
const obj2 = {a: 3};
const bar = foo.call(obj1);
console.log(bar.call(obj2)) // 2
```
* 箭头函数的 this 就是外出代码块的 this，所以就是 foo 的 this，所以是 obj1
</details>
<br><br>