---
layout: post
category : javascript
tagline: "Supporting tagline"
tags : [creat, object, tutorial]
title : 创建对象
articleType : blog
---
{% include JB/setup %}


```javascript
/*最简单的办法，创建Object实例，再对其添加属性和方法。*/
var person = new Object();
person.name = "Nicholas";
person.age = 29;
person.job = "Software Engineer";

person.sayName = function() {
	alert(this.name);
};
person.sayName();



/*工厂模式*/

function createPerson(name, age, job) {
	var o = new Object();
	o.name = name;
	o.age = age;
	o.job = job;
	o.sayName = function() {
		alert(this.name);
	};
	return o;
}

var person1 = createPerson("Nicholas", 29, "Software Engineer");
var person2 = createPerson("Greg", 27, "Doctor");



/*构造函数模式*/

function Person(name, age, job) {
	this.name = name;
	this.age = age;
	this.job = job;
	this.sayName = function() {
		alert(this.name);
	};
}

var person1 = new Person("Nicholas", 29, "Software Engineer");
var person2 = new Person("Greg", 27, "Doctor");

person1.sayName(); //"Nicholas" 
person2.sayName(); //"Greg" 
/*
1、没有显式地创建对象;
2、直接将属性和方法赋给了this对象;
3、没有return语句;
*/



/*原型模式*/

function Person() {}

Person.prototype.name = "Nicholas";
Person.prototype.age = 29;
Person.prototype.job = "Software Engineer";
Person.prototype.sayName = function() {
	alert(this.name);
};

var person1 = new Person();
person1.sayName(); //"Nicholas" 

var person2 = new Person();
person2.sayName(); //"Nicholas"

alert(person1.sayName == person2.sayName); //true
/*
	无论什么时候，只要创建了一个新函数，就会根据一组特定的规则为该函数创建一个prototype属性。
在默认情况下，所有prototype（原型）属性都会自动获得一个constructor(构造函数)属性，原型的
构造函数属性指向这个原型所在的新函数。
    其实，原型只不过是实例和构造函数共有的一个属性而已使用hasOwnPrototype()方法可以检测
一个属性是存在于实例中，还是存在于原型中。只有属性是存在于实例中，则返回true。
*/



/*更简单的原型语法,用对象字面量形式创建新对象*/

function Person() {}

Person.prototype = {
	constructor: Person,
	name: "Nicholas",
	age: 29,
	job: "Software Engineer",
	sayName: function() {
		alert(this.name);
	}
};

var person = new Person();

alert(person instanceof Object); //true
alert(person instanceof Person); //true
alert(person.constructor == Person); //false
alert(person.constructor == Object); //true

//可以通过该方法将constructor指向Person
Person.prototype = {
	constructor: Person
};
/*
   在这里，该构造函数的prototype（原型）中的constructor属性不再指向Person了。
每创建一个函数，就会同时创建它的prototype对象，这个对象也会自动获得constructor属性。
而在这里使用的语法，本质上完全重写了默认的prototype对象，因此constructor属性也就变成
了新对象的constructor属性（指向object构造函数），不再指向Person函数。
*/



/*原型的动态性*/
var person = new Person();

Person.prototype.sayHi = function() {
	alert('hi');
};

person.sayHi(); // "hi"

//重写原型对象

function Person() {}

var person = new Person();

Person.prototype = {
	constructor: Person,
	name: 'Nicholas',
	age: 29,
	job: 'Software Engineer',
	sayName: function() {
		alert(this.name);
	}
};

person.sayName(); //error
/*
以上先创建了Person的一个实例，保存在变量person中，后在原型中添加一个新方法，可以实时在
实例中体现出来。
由此可见，在创建新对象之后，对该构造函数原型进行重写，老的实例对象还是指向原来的原型，
而重写后的原型对象中的成员将不会体现在该实例中，除非调换顺序。
*/



/*原生对象的原型*/
string.prototype.startsWith = function(text) {
	return this.indexOf(text) == 0;
};

var msg = 'Hello World!';
alert(msg.startsWith('Hello')); //true



/*组合使用构造函数模式和原型模式*/

function Person(name, age, job) {
	this.name = name;
	this.age = age;
	this.job = job;
	this.friends = ['Shelby', 'Court'];
}

Person.prototype = {
	constructor: Person,
	sayName: function() {
		alert(this.name);
	}
}

var person1 = new Person('Nicholas', 29, 'Software Engineer');
var person2 = new Person('Greg', 29, 'Doctor');

person1.friends.push('Van');
alert(person1.friends);
alert(person2.friends);
alert(person1.friends == person2.friends);
alert(person1.sayName == person2.sayName);



/*动态原型模式,根据实际情况添加方法到原型，原型的修改能实时反映到实例中*/

function Person(name, age, job) {
	//属性
	this.name = name;
	this.age = age;
	this.job = job;

	//方法
	if (typeof this.sayName != "function") {

		Person.prototype.sayName = function() {
			alert(this.name);
		};
	}
}

var person = new Person('Nicholas', 29, 'Software Engineer');
person.sayName();
//值得注意的是，该原型不能用对象字面量重写，如果重写，实例不会实时反映原型的变化，他们还是联系在原来的原型上的。



/*寄生构造函数模式*/

function Person(name, age, job) {
	var o = new Object();
	o.name = name;
	o.age = age;
	o.job = job;
	o.sayName = function() {
		alert(this.name);
	};
	return o;
}

var person = new Person('Nicholas', 29, 'Software Engineer');
person.sayName(); //'Nicholas'

function SpecialArray() {
	//创建数组
	var values = new Array();

	//添加值
	values.push.apply(values, arguments);

	//添加方法
	values.toPipedString = function() {
		return this.join('|');
	};

	//返回数组
	return values;
}

var colors = new SpecialArray('red', 'blue', 'green');
alert(colors.toPipedString()); //'red|blue|green'
/*
返回的对象与构造函数或者与构造函数的原型属性之间没有关系，构造函数返回的对象与在工作函数
外部创建的对象没有什么不同。该模式使用较少
*/



/*稳妥构造函数模式*/

function Person(name, age, job) {
	//创建要返回的对象
	var o = new Object();

	//可以在这里定义私有变量和函数

	//添加方法
	o.sayName = function() {
		alert(name);
	};

	//返回对象
	return o;
}



function Person() {}

Person.prototype.name = "Nicholas";
Person.prototype.age = 29;
Person.prototype.job = "Software Engineer";
Person.prototype.sayName = function() {
	alert(this.name);
};

var person1 = new Person();
var person2 = new Person();

alert(person1.hasOwnProperty("name")); //false
alert("name" in person1); //true

person1.name = "Greg";
alert(person1.name); //Greg



function Person() {}

Person.prototype.name = "Nicholas";
Person.prototype.age = 29;
Person.prototype.job = "Software Engineer";
Person.prototype.sayName = function() {
	alert(this.name);
};

var person1 = new Person();
var person2 = new Person();

alert(person1.hasOwnProperty("name")); // false
alert("name" in person1); // true



//寄生构造函数模式,在其他模式下不建议采用此模式

function Person(name, age, job) {
	var o = new Object();
	o.name = name;
	o.age = age;
	o.job = job;
	o.sayName = function() {
		alert(this.name);
	};
	return o;
}

var person = new Person("Nicholas", 29, "Software Engineer");
person.sayName();



//稳妥构造函数模式



/*
   JS根本上是和对象相关的。数组是对象、函数是对象、对象是对象。那什么是对象呢？
对象是名-值对的集合。名是字符串，值可以是字符串、数字、布尔值或者对象（包括数组和函数）。
通常对象是像哈希表一样执行的，这样便于值的快速检索。
   如果值是函数，我们可以认为这是一个方法，当一个对象的方法被调用的时候，this变量就设置为这个对象。
方法就可以通过this变量来访问实例变量。
    对象可以由用来初始化对象的构造函数生成。构造函数提供了其他语言中类所提供的特性，包括静态变量和方法。

公共：
   对象的成员全部是公共成员，任何函数都可以访问、修改、删除或者新增成员。有两种向新对象中添加成员的方法：
*/



//在构造函数中添加

function Container(param) {
	this.member = param;
}

var myContainer = new Container('abc');


//在原型中添加

Container.prototype.stamp = function(string) {
	return this.member + string;
}

myContainer.stamp('def')
```
[栅格计算](http://leegorous.net/tools/grids.html)
