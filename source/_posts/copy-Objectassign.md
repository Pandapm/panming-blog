---
title: JavaScript对象拷贝与Object.assign
date: 2017-11-03 19:09:41
tags: [JavaScript, 对象拷贝]
categories: Web前端 
---
## 深拷贝与浅拷贝
在 JavaScript 中，对于基本数据类型（undefined、null、boolean、number、string）来说，在变量中存储的就是这个变量本身的值，复制是对**值的复制**，不存在深浅之说。但C系语言的共同特点中有，存储引用类型（对象），实际中在变量里存的是它的地址。因此对 JavaScript 中的复杂数据类型（object）来说，也会有浅拷贝和深拷贝的概念：**浅拷贝指两个不同的变量存的是同一个对象的地址，即两个变量指向同一块内存区域；深拷贝则是重新分配了一块内存区域来存储复制后的对象，两个变量存的是真正的两个互不影响的变量**。
<!-- more -->
p.s. 有些资料认为**浅拷贝是重新分配内存，并把原对象中的各个属性进行依次复制而不进行递归复制属性值是对象的情况，也就是只复制对象的最外面一层**。本文将这种情况归于“**深拷贝和浅拷贝的中间情况**”，文中以“**是否划分新的内存**”为界限划分深浅拷贝，这种划分方式与 C/C++、C#、Java 等C系语言保持概念一致。

## 浅拷贝在JavaScript中的实现
浅拷贝在js中很简单，例如：
```javascript
let objA = {
	name: '对象A',
	content: '我是A'
};
let copyA = objA;

console.log(objA.name);  // ==> "对象A"
console.log(copyA.name);  // ==> "对象A"
```
如此即得到了objA的一份浅拷贝copyA，由于指向的是同一个对象，因此在修改objA的同时也是修改了copyA，反之亦然。

## Object.assign()

Object.assign(target, ...sources) MDN上对该方法的描述是 `将所有可枚举属性的值从一个或多个源对象复制到目标对象`，String类型和 Symbol 类型的属性都会被拷贝, 并且**该方法会忽略值为 `null` 或者 `undefined` 的源对象**。例如：
```javascript
var o1 = { a: 1 };
var o2 = { [Symbol('foo')]: 2 };

var obj = Object.assign({}, o1, o2);
console.log(obj); // { a : 1, [Symbol("foo")]: 2 } (cf. bug 1207182 on Firefox)
Object.getOwnPropertySymbols(obj); // [Symbol(foo)]
```
### Object.assign 的深拷贝与浅拷贝

注意前面说的是可枚举属性，这是一个介于完全的深拷贝和完全的浅拷贝之间的方法：如果我们把它的第一个参数target设置为一个空对象 `{}`，同时保证剩余的源对象sources中的属性类型不包含引用类型，则该方法的返回值就是一个与源对象相同的但并不在同一块内存空间另一个对象，即获得了源对象的**深拷贝**。但是，如果源对象的属性中包含某个对象，也就是这个属性的值指向某个对象，就像下面这样：
```javascript
var obj = {
	name: 'obj name',
	content: {
		a: 1,
		b: 2
	}
};
```
则使用 `Object.assign({}, obj)` 时，返回的目标对象中的content属性与源对象obj中的content属性指向的同一块内存区域，即对obj下的content属性进行了**浅拷贝**。因此针对深拷贝，需要使用其他方法，比如自己实现一个深拷贝的方法，或者使用 `JSON.parse(JSON.stringify(obj))`， 因为 Object.assign()拷贝的是属性值。

### Object.assign 的属性覆盖

如果目标对象中的属性具有相同的键，则属性将被源中的属性覆盖。后来的源的属性将类似地覆盖早先的属性，因此可以用来合并对象（常用的一个场景是使用reducers更新React应用的状态）。
```javascript
var o1 = { a: 1, b: 1, c: 1 };
var o2 = { b: 2, c: 2 };
var o3 = { c: 3 };

var obj = Object.assign({}, o1, o2, o3);
console.log(obj); // { a: 1, b: 2, c: 3 }
```
### 关于对象的继承属性和不可枚举属性

前文有提到，`Object.assign` 拷贝的是对象的可枚举属性，该方法使用源对象的 `[[Get]]` 和目标对象的 `[[Set]]`，所以它会调用相关 `getter` 和 `setter`。因此，不如说它是分配属性，而不仅仅是复制或定义新的属性。如果合并源包含 `getter`，这可能使其不适合将新属性合并到原型中，将属性定义（包括其可枚举性）复制到原型应使用`Object.getOwnPropertyDescriptor()` 和 `Object.defineProperty()` ，因此 `Object.assign` 不能拷贝对象的继承属性，例如：
```javascript
var obj = Object.create({foo: 1}, { // foo 是个继承属性。
    bar: {
        value: 2  // bar 是个不可枚举属性。
    },
    baz: {
        value: 3,
        enumerable: true  // baz 是个自身可枚举属性。
    }
});

var copy = Object.assign({}, obj);
console.log(copy); // { baz: 3 }
```

### 当源对象是原始数据类型时

ECMAScript 有 5 种原始类型（primitive type）:  `Undefined`、`Null`、`Boolean`、`Number` 和 `String`。当Object.assign的源对象是原始类型时，源对象会被包装成“对象”，对应的键是它在源对象中的索引值：
```javascript
var v1 = "abc";
var v2 = true;
var v3 = 10;
var v4 = Symbol("foo")

var obj = Object.assign({}, v1, null, v2, undefined, v3, v4); 
// 原始类型会被包装，null 和 undefined 会被忽略。
// 注意，只有字符串的包装对象才可能有自身可枚举属性。
console.log(obj); // { "0": "a", "1": "b", "2": "c" }
```

### 当拷贝过程中发生异常时
在出现错误的情况下，例如，如果属性不可写，会引发TypeError，异常会打断后续的拷贝任务。如果在引发错误之前添加了任何属性，则可以更改target对象。
```javascript
var target = Object.defineProperty({}, "foo", {
    value: 1,
    writable: false
}); // target 的 foo 属性是个只读属性。

Object.assign(target, {bar: 2}, {foo2: 3, foo: 3, foo3: 3}, {baz: 4});
// TypeError: "foo" is read-only
// 注意这个异常是在拷贝第二个源对象的第二个属性时发生的。

console.log(target.bar);  // 2，说明第一个源对象拷贝成功了。
console.log(target.foo2); // 3，说明第二个源对象的第一个属性也拷贝成功了。
console.log(target.foo);  // 1，只读属性不能被覆盖，所以第二个源对象的第二个属性拷贝失败了。
console.log(target.foo3); // undefined，异常之后 assign 方法就退出了，第三个属性是不会被拷贝到的。
console.log(target.baz);  // undefined，第三个源对象更是不会被拷贝到的。
```