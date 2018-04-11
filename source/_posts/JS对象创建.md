---
title: JS的对象创建
date: 2018-03-31 09:59:38
tags:
---

> 这些基本是面试常问的问题，下面都是我从JS高程这本书上的总结
<!--more-->
## 工厂模式

```javascript 

function createPerson(name,age,gender){
  var obj=new Object();
  obj.name=name;
  obj.age=age;
  obj.gender=gender;
  obj.sayName=function(){
    console.log(this.name);
  }
  return obj;
}

var person1= createPerson("张三",23,"男");
var person2= createPerson("小李",56,"女");

```

`缺点`：无法解决对象识别的问题

## 构造函数模式
```javascript 

function Person(name,age,gender){
  this.name=name;
  this.age=age;
  this.gender=gender;
  this.sayName=function(){
    console.log(this.name);
  }
}

var person1= new Person("xxx",23,"男");
var person2= new Person("yy",44,"男");

```
当使用`new`执行该构造函数时经历了以下4个步骤：

1.  创建一个对象
2.  把构造函数的作用域赋给新对象（this就指向了新对象）
3.  执行构造函数中的代码
4. 返回新对象

这个方法解决了创建对象没有类型的问题

`person1`和`person2`分别保存着一份Person实例，两个对象都有一个`constructor`指向`Person`
```javascript
person1.constructor==Person; // true
person2.constructor==Person; // true
person1 instanceof Person; // tru
person2 instanceof Person; // true


```
这证明构造函数模式可以将它的实例标识一种特定的类型，这正是构造函数优于工厂模式的地方

`缺点`

* 构造函数和普通函数没有什么区别，当不使用`new`调用时会出现意想不到的错误
* 构造函数的每个方法都要在每个实例上重新创建一遍


## 原型模式

```javascript 

function Person(){}

Person.prototype.name="xxx:";
Person.prototype.age=32;
Person.prototype.sayName=function(){
  console.log(this.name);
}

var person1= new Person();

var person2= new Person();

person1.sayName==preson2.sayName //true


```

![WX20180331-143831@2x](http://p6fy6gb44.bkt.clouddn.com/WX20180331-143831@2x.png)

* 每一个函数都有一个prototype
* 每一个prototype属性指向该函数的原型
* protoype原型中有一个constructor属性，它包含一个指向prototype属性所在函数的指针
* 由构造函数所创建的实例都包含一个指向构造函数原型对象的指针 `[[Prototype]]`，也叫`__proto__`

```javascript

Person.prototype.isPrototypeOf(person1) // true
Object.getPrototypeOf(person1)==Person.prototype // true

```
以上两个方法可以判断实例和原型的关系


使用原型继承还以下几点要注意：

* 如果直接在实例上更同名属性，不会改变原型上的属性，只会隐藏原型上的属性
* 更改之后可以delete删除实例上的属性，但不会影响原型

> 可以通过`person1.Object.hasOwnProperty("name")`判断一个属性是不是实例属性，如果是则返回true,而`in操作符`无论是实例属性还是原型属性，只要存在都会返回true

**Object.keys()** 返回实例对象上所有可枚举的实例属性的字符串数组
**Object.getOwnPropertyNames()** 返回实例对象上的所有实例属性，不管是可不可枚举


## 组合使用构造函数和原型模式

这个就是结合了构造函数和原型的优点
* 构造函数用于定义实例属性
* 原型用于定义方法和共享的属性

这也是最常用的继承方法


## 动态原型模式

```javascript

function Person(name,age,gender){
  this.name=name;
  this.age=age;
  this.gender=gender;
  if(typeof this.sayName !=="function"){
    Person.prototype.sayName=function(){
      console.log(this.name);
    }
  }
}

var person1=new Person("xxx",22,"男");
var person2=new Person("ccc",88,"女");
person1.sayName==person2.sayName;// true;


```

原理：通过在初使化时才进行原型添加；可以说比较完美；

## 寄生构造函数模式

```javascript

function Person(name,age,gender){
  var obj=new Object();
  obj.name=name;
  obj.age=age;
  obj.gender=gender;
  obj.sayName=function(){
    console.log(this.name);
  }
  return obj;
}
var person1 =new Person("xx",22,"女")


```
除了调用方式和工厂模式多了一个new之外没有什么任何不同
原理：改写了new的默认返回值
主要应用于对一个已存在的类型增加一些特殊的方法
应用举例

```javascript

function SpecialArray(){
  var values=new Array();
  values.push.apply(values,arguments);
  values.toPipedString(){
    return this.join("|");
  }
  return values;
}

var sa=new SpecialArray("red","green","yellow");

sa.toPipedString(); // "red"|"green"|"yellow"

```
