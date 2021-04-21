---
title: ECMAScript 6 Features - Part 1
date: 2021-04-20 22:49:06
tags:
 - javascript
 - programming
---

A brief overview of some of the latest features available in ECMAScript 6, with examples of how they were written previously in ECMAScript 5, if even possible.

## Constants

Constants are immutable variables or variables which cannot be re-assigned new content.

```javascript
const PI = 3.141593
```

## Scoping

### Block-Scoped Variables

ECMAScript 6:
```javascript
for (let i = 0; i < a.length; i++) {
    let x = a[i];
    …
}
for (let i = 0; i < b.length; i++) {
    let y = b[i];
    …
}

let callbacks = [];
for (let i = 0; i <= 2; i++) {
    callbacks[i] = function () { return i * 2; };
}
callbacks[0]() === 0;
callbacks[1]() === 2;
callbacks[2]() === 4;
```

ECMAScript 5:
```javascript
var i, x, y;
for (i = 0; i < a.length; i++) {
    x = a[i];
    …
}
for (i = 0; i < b.length; i++) {
    y = b[i];
    …
}

var callbacks = [];
for (var i = 0; i <= 2; i++) {
    (function (i) {
        callbacks[i] = function() { return i * 2; };
    })(i);
}
callbacks[0]() === 0;
callbacks[1]() === 2;
callbacks[2]() === 4;
```

### Block-Scoped Functions

ECMAScript 6:
```javascript
{
    function blah () { return 1; }
    blah() === 1;
    {
        function blah () { return 2; }
        blah() === 2;
    }
    blah() === 1;
}
```

ECMAScript 5:
```javascript
//  only in ES5 with the help of block-scope emulating
//  function scopes and function expressions
(function () {
    var blah = function () { return 1; }
    blah() === 1;
    (function () {
        var blah = function () { return 2; }
        blah() === 2;
    })();
    blah() === 1;
})();
```

## Arrow Functions

### Expression Bodies

More expressive closure syntax.

ECMAScript 6:
```javascript
odds  = evens.map(v => v + 1);
pairs = evens.map(v => ({ even: v, odd: v + 1 }));
nums  = evens.map((v, i) => v + i);
```

ECMAScript 5:
```javascript
odds  = evens.map(function (v) { return v + 1; });
pairs = evens.map(function (v) { return { even: v, odd: v + 1 }; });
nums  = evens.map(function (v, i) { return v + i; });
```

### Statement Bodies

More expressive closure syntax.

ECMAScript 6:
```javascript
nums.forEach(v => {
   if (v % 5 === 0)
       fives.push(v);
})
```

ECMAScript 5:
```javascript
nums.forEach(function (v) {
   if (v % 5 === 0)
       fives.push(v);
});
```

### Lexical _this_

More intuitive handling of current object context.

ECMAScript 6:
```javascript
this.nums.forEach((v) => {
    if (v % 5 === 0)
        this.fives.push(v);
});
```

ECMAScript 5:
```javascript
//  variant 1
var self = this;
this.nums.forEach(function (v) {
    if (v % 5 === 0)
        self.fives.push(v);
});

//  variant 2
this.nums.forEach(function (v) {
    if (v % 5 === 0)
        this.fives.push(v);
}, this);

//  variant 3 (since ECMAScript 5.1 only)
this.nums.forEach(function (v) {
    if (v % 5 === 0)
        this.fives.push(v);
}.bind(this));
```

## Extended Parameter Handling
### Default Parameter Values

Simple and intuitive default values for function parameters.

ECMAScript 6:
```javascript
function blah (x, y = 7, z = 42) {
    return x + y + z;
}
blah(1) === 50;
```

ECMAScript 5:
```javascript
function blah (x, y, z) {
    if (y === undefined)
        y = 7;
    if (z === undefined)
        z = 42;
    return x + y + z;
};
blah(1) === 50;
```

### Rest Parameter

Aggregation of remaining arguments into single parameter of variadic functions (a function where the total number of parameters are unknown and can be adjusted at the time the method is called).

ECMAScript 6:
```javascript
function blah (x, y, ...a) {
    return (x + y) * a.length;
}
blah(1, 2, "hello", true, 7) === 9;
```

ECMAScript 5:
```javascript
function blah (x, y) {
    var a = Array.prototype.slice.call(arguments, 2);
    return (x + y) * a.length;
};
blah(1, 2, "hello", true, 7) === 9;
```

### Spread Operator

Spreading of elements of an iterable collections (like an array or string) into both literal elements and individual function parameters.

ECMAScript 6:
```javascript
var params = [ "hello", true, 7 ];
var other = [ 1, 2, ...params ]; // [ 1, 2, "hello", true, 7 ]

function blah (x, y, ...a) {
    return (x + y) * a.length;
}
blah(1, 2, ...params) === 9;

var str = "blah";
var chars = [ ...str ]; // [ "f", "o", "o" ]
```

ECMAScript 5:
```javascript
var params = [ "hello", true, 7 ];
var other = [ 1, 2 ].concat(params); // [ 1, 2, "hello", true, 7 ]

function blah (x, y) {
    var a = Array.prototype.slice.call(arguments, 2);
    return (x + y) * a.length;
};
f.apply(undefined, [ 1, 2 ].concat(params)) === 9;

var str = "blah";
var chars = str.split(""); // [ "f", "o", "o" ]
```

## Template Literals

### String Interpolation

Intuitive expression interpolation for single-line and multi-line strings.

ECMAScript 6:
```javascript
var customer = { name: "Foo" };
var card = { amount: 7, product: "Bar", unitprice: 42 };
var message = `Hello ${customer.name},
want to buy ${card.amount} ${card.product} for
a total of ${card.amount * card.unitprice} bucks?`;
```

ECMAScript 5:
```javascript
var customer = { name: "Foo" };
var card = { amount: 7, product: "Bar", unitprice: 42 };
var message = "Hello " + customer.name + ",\n" +
"want to buy " + card.amount + " " + card.product + " for\n" +
"a total of " + (card.amount * card.unitprice) + " bucks?";
```

### Custom Interpolation

Flexible expression interpolation for arbitrary methods.

ECMAScript 6:
```javascript
get`http://example.com/foo?bar=${bar + baz}&blah=${blah}`;
```

ECMAScript 5:

```javascript
get([ "http://example.com/foo?bar=", "&blah=", "" ],bar + baz, blah);
```

### Raw String Access

Access the raw template string content (backslashes are not interpreted).

ECMAScript 6:
```javascript
function blah (strings, ...values) {
    strings[0] === "foo\n";
    strings[1] === "bar";
    strings.raw[0] === "foo\\n";
    strings.raw[1] === "bar";
    values[0] === 42;
}
blah`foo\n${ 42 }bar`

String.raw`foo\n${ 42 }bar` === "foo\\n42bar";
```

ECMAScript 5:

```javascript
//  not possible in ES5
```

## Enhanced Object Properties

### Property Shorthand

Shorter syntax for common object property definition idiom.

ECMAScript 6:
```javascript
let x = 0, y = 0
obj = { x, y }
```

ECMAScript 5:

```javascript
var x = 0, y = 0;
obj = { x: x, y: y }
```

### Computed Property Names

Support for computed names in object property definitions.

ECMAScript 6:
```javascript
let obj = {
    foo: "bar",
    [ "baz" + quux() ]: 42
}
```

ECMAScript 5:

```javascript
var obj = {
    foo: "bar"
};
obj[ "baz" + quux() ] = 42;
```

### Method Properties

Support for method notation in object property definitions, for both regular functions and generator functions.

ECMAScript 6:
```javascript
obj = {
    foo (a, b) {
        ...
    },
    bar (x, y) {
        ...
    },
    *quux (x, y) {
        ...
    }
}
```

ECMAScript 5:

```javascript
obj = {
    foo: function (a, b) {
        ...
    },
    bar: function (x, y) {
        ...
    }, 
    // quux: no equivalent in ES5
}
```

## Destructuring Assignment

Destructuring allows you to assign the properties of an object or array to variables using syntax that looks similar to object or array literals.  This syntax can be extremely terse, while still exhibiting more clarity than the traditional property access.

### Array Matching
Intuitive and flexible destructuring of Arrays into individual variables during assignment.

ECMAScript 6:
```javascript
var list = [ 1, 2, 3 ]
var [ a, , b ] = list
[ b, a ] = [ a, b ]
```

ECMAScript 5:
```javascript
var list = [ 1, 2, 3 ];
var a = list[0], b = list[2];
var tmp = a; a = b; b = tmp;
```

### Object Matching, Shorthand
Intuitive and flexible destructuring of Objects into individual variables during assignment.

AST = Abstract Syntax Tree, which are data structures used in compilers. [Further reading](https://jotadeveloper.medium.com/abstract-syntax-trees-on-javascript-534e33361fc7)

ECMAScript 6:
```javascript
var { op, lhs, rhs } = getASTNode()
```

ECMAScript 5:
```javascript
var tmp = getASTNode();
var op = tmp.op;
var lhs = tmp.lhs;
var rhs = tmp.rhs;
```

### Object Matching, Deep Matching
Intuitive and flexibile destructuring of Objects into individual variables during assignment.

ECMAScript 6:
```javascript
var { op: a, lhs: { op: b }, rhs: c } = getASTNode()
```

ECMAScript 5:
```javascript
var tmp = getASTNode();
var op = tmp.op;
var lhs = tmp.lhs;
var rhs = tmp.rhs;
```

### Object and Array Matching, Default Values
ECMAScript 6:
```javascript
var obj = { a: 1 }
var list = [ 1 ]
var { a, b = 2 } = obj
var [ x, y = 2 ] = list
```

ECMAScript 5:
```javascript
var obj = { a: 1 };
var list = [ 1 ];
var a = obj.a;
var b = obj === undefined ? 2 : obj.b;
var x = list[0];
var y = list[1] === undefined ? 2 : list[1];
```

### Parameter Context Matching
Intuitive and flexible destructuring of Arrays and Objects into individual parameters during function calls.

ECMAScript 6:
```javascript
function f ([ name, val ]) {
    console.log(name, val)
}
function g ({ name: n, val: v }) {
    console.log(n, v)
}
function h ({ name, val }) {
    console.log(name, val)
}

f([ "bar", 42 ])
g({ name: "foo", val: 7 })
h({ name: "bar", val: 42 })
```

ECMAScript 5:
```javascript
function f (arg) {
    var name = arg[0];
    var val = arg[1];
    console.log(name, val);
}
function g (arg) {
    var n = arg.name;
    var v = arg.val;
    console.log(n, v);
}
function h (arg) {
    var name = arg.name;
    var val = arg.val;
    console.log(n, v);
}
f([ "bar", 42 ]);
g({ name: "foo", val: 7 });
h({ name: "bar", val: 42 });
```

### Fail-Soft Destructuring
Fail-soft destructuring, optionally with defaults

ECMAScript 6:
```javascript
var list = [ 7, 42 ];
var [ a = 1, b = 2, c = 3, d ] = list
a === 7
b === 42
c === 3
d === undefined
```

ECMAScript 5:
```javascript
var list = [ 7, 42 ];
var a = typeof list[0] !== "undefined" ? list[0] : 1;
var b = typeof list[1] !== "undefined" ? list[1] : 2;
var c = typeof list[2] !== "undefined" ? list[2] : 3;
var d = typeof list[3] !== "undefined" ? list[3] : undefined;
a === 7;
b === 42;
c === 3;
d === undefined;
```

## Modules

### Value Export/Import
Support for exporting/importing values from/to modules without global namespace pollution.

ECMAScript 6:
```javascript
// lib/math.js
export function sum (x, y) { return x + y }
export var pi = 3.141593

// someApp.js
import * as math from "lib/math.js"
console.log("2π = " + math.sum(math.pi, math.pi))

// otherApp.js
import { sum, pi } from "lib/math.js"
console.log("2π = " + sum(pi, pi))
```

ECMAScript 5:
```javascript
//  lib/math.js
LibMath = {};
LibMath.sum = function (x, y) { return x + y };
LibMath.pi = 3.141593;

//  someApp.js
var math = LibMath;
console.log("2π = " + math.sum(math.pi, math.pi));

//  otherApp.js
var sum = LibMath.sum, pi = LibMath.pi;
console.log("2π = " + sum(pi, pi));
```

### Default & Wildcard
Marking a value as the default exported value and mass-mixin of values.

ECMAScript 6:
```javascript
// lib/mathplusplus.js
export * from "lib/math"
export var e = 2.71828182846
export default (x) => Math.exp(x)

// someApp.js
import exp, { pi, e } from "lib/mathplusplus"
console.log("e^{π} = " + exp(pi))
```

ECMAScript 5:
```javascript
//  lib/mathplusplus.js
LibMathPP = {};
for (symbol in LibMath)
    if (LibMath.hasOwnProperty(symbol))
        LibMathPP[symbol] = LibMath[symbol];
LibMathPP.e = 2.71828182846;
LibMathPP.exp = function (x) { return Math.exp(x) };

//  someApp.js
var exp = LibMathPP.exp, pi = LibMathPP.pi, e = LibMathPP.e;
console.log("e^{π} = " + exp(pi));
```

## Classes

### Class Definition
More intuitive, OOP-style and boilerplate-free classes.

ECMAScript 6:
```javascript
class Shape {
    constructor (id, x, y) {
        this.id = id
        this.move(x, y)
    }
    move (x, y) {
        this.x = x
        this.y = y
    }
}
```

ECMAScript 5:
```javascript
var Shape = function (id, x, y) {
    this.id = id;
    this.move(x, y);
};
Shape.prototype.move = function (x, y) {
    this.x = x;
    this.y = y;
};
```

### Class Inheritance
More intuitive, OOP-style and boilerplate free inheritance.

ECMAScript 6:
```javascript
class Rectangle extends Shape {
    constructor (id, x, y, width, height) {
        super(id, x, y)
        this.width = width
        this.height = height
    }
}
class Circle extends Shape {
    constructor (id, x, y, radius) {
        super(id, x, y)
        this.radius = radius
    }
}
```

ECMAScript 5:
```javascript
var Rectangle = function (id, x, y, width, height) {
    Shape.call(this, id, x, y);
    this.width  = width;
    this.height = height;
};
Rectangle.prototype = Object.create(Shape.prototype);
Rectangle.prototype.constructor = Rectangle;
var Circle = function (id, x, y, radius) {
    Shape.call(this, id, x, y);
    this.radius = radius;
};
Circle.prototype = Object.create(Shape.prototype);
Circle.prototype.constructor = Circle;
```

### Class Inheritance from Expressions
Support for mixin-style inheritance by extending from expressions yielding function objects.  The generic aggregation function is usually provided by a library such as [this](https://github.com/rse/aggregation).

ECMAScript 6:
```javascript
var aggregation = (baseClass, ...mixins) => {
    let base = class _Combined extends baseClass {
        constructor (...args) {
            super(...args)
            mixins.forEach((mixin) => {
                mixin.prototype.initializer.call(this)
            })
        }
    }
    let copyProps = (target, source) => {
        Object.getOwnPropertyNames(source)
        .concat(Object.getOwnPropertySymbols(source))
        .forEach((prop) => {
            if (prop.match(/^(?:constructor|prototype|arguments|caller|name|bind|call|apply|toString|length)$/))
                return
            Object.defineProperty(target, prop, Object.getOwnPropertyDescriptor(source, prop))
        })
    }
    mixins.forEach((mixin) => {
        copyProps(base.prototype, mixin,prototype)
        copyProps(base, mixin)
    })
    return base
}

class Colored {
    initializer () { this._color = "white" }
    get color () { return this._color }
    set color (v) { this._color = v }
}

class ZCoord {
    initializer () { this._z = 0 }
    get z () { return this._z }
    set z (v) { this._z = v }
}

class Shape {
    constructor (x, y) { this._x = x; this._y = y }
    get x () { return this._x }
    set x (v) { this._x = v }
    get y () { return this._y }
    set y (v) { this._y = v }
}

class Rectangle extends aggregation(Shape, Colored, ZCoord) {}

var rect = new Rectangle(7, 42)
rect.z = 1000
rect.color = "red"
console.log(rect.x, rect.y, rect.z, rect.color)
```

ECMAScript 5:
```javascript
var aggregation = function (baseClass, mixins) {
    var base = function () {
        baseClass.apply(this, arguments);
        mixins.forEach(function (mixin) {
            mixin.prototype.initializer.call(this);
        }.bind(this));
    };
    base.prototype = Object.create(baseClass.prototype);
    base.prototype.constructor = base;
    var copyProps = function (target, source) {
        Object.getOwnPropertyNames(source).forEach(function (prop) {
            if (prop.match(/^(?:constructor|prototype|arguments|caller|name|bind|call|apply|toString|length)$/))
                return
            Object.defineProperty(target, prop, Object.getOwnPropertyDescriptor(source, prop))
        })
    }
    mixins.forEach(function (mixin) {
        copyProps(base.prototype, mixin.prototype);
        copyProps(base, mixin);
    });
    return base;
};

var Colored = function () {};
Colored.prototype = {
    initializer: function ()  { this._color = "white"; },
    getColor:    function ()  { return this._color; },
    setColor:    function (v) { this._color = v; }
};

var ZCoord = function () {};
ZCoord.prototype = {
    initializer: function ()  { this._z = 0; },
    getZ:        function ()  { return this._z; },
    setZ:        function (v) { this._z = v; }
};

var Shape = function (x, y) {
    this._x = x; this._y = y;
};
Shape.prototype = {
    getX: function ()  { return this._x; },
    setX: function (v) { this._x = v; },
    getY: function ()  { return this._y; },
    setY: function (v) { this._y = v; }
}

var _Combined = aggregation(Shape, [ Colored, ZCoord ]);
var Rectangle = function (x, y) {
    _Combined.call(this, x, y);
};
Rectangle.prototype = Object.create(_Combined.prototype);
Rectangle.prototype.constructor = Rectangle;

var rect = new Rectangle(7, 42);
rect.setZ(1000);
rect.setColor("red");
console.log(rect.getX(), rect.getY(), rect.getZ(), rect.getColor());
```

### Base Class Access
Intuitive access to base class constructor and methods.

ECMAScript 6
```javascript
class Shape {
    ...
    toString () {
        return `Shape(${this.id})`
    }
}
class Rectangle extends Shape {
    constructor (id, x, y, width, height) {
        super(id, x, y)
        ...
    }
    toString() {
        return "Rectangle > " + super.toString()
    }
}
class Circle extends Shape {
    constructor (id, x, y, radius) {
        super(id, x, y)
        ...
    }
    toString () {
        return "Circle > " + super.toString()
    }
}
```

ECMAScript 5
```javascript
var Shape = function (id, x, y) {
    …
};
Shape.prototype.toString = function (x, y) {
    return "Shape(" + this.id + ")"
};
var Rectangle = function (id, x, y, width, height) {
    Shape.call(this, id, x, y);
    …
};
Rectangle.prototype.toString = function () {
    return "Rectangle > " + Shape.prototype.toString.call(this);
};
var Circle = function (id, x, y, radius) {
    Shape.call(this, id, x, y);
    …
};
Circle.prototype.toString = function () {
    return "Circle > " + Shape.prototype.toString.call(this);
};
```

### Static Members
Simple support for static class members.

ECMAScript 6
```javascript
class Rectangle extends Shape {
    ...
    static defaultRectangle () {
        return new Rectangle("default", 0, 0, 100, 100)
    }
}
class Circle extends Shape {
    ...
    static defaultCircle () {
        return new Circle("default", 0, 0, 100)
    }
}
var defRectangle = Rectangle.defaultRectangle()
var defCircle = Circle.defaultCircle()
```

ECMAScript 5
```javascript
var Rectangle = function (id, x, y, width, height) {
    …
};
Rectangle.defaultRectangle = function () {
    return new Rectangle("default", 0, 0, 100, 100);
};
var Circle = function (id, x, y, width, height) {
    …
};
Circle.defaultCircle = function () {
    return new Circle("default", 0, 0, 100);
};
var defRectangle = Rectangle.defaultRectangle();
var defCircle    = Circle.defaultCircle();
```

### Getter/Setter
Getter/Setter also directly within classes and not just within object initializers

ECMAScript 6
```javascript
class Rectangle {
    constructor (width, height) {
        this._width = width
        this._height = height
    }
    set width (width) { this._width = width }
    get width () { return this._width }
    set height (height) { this._height = height }
    get height () { return this._height }
    get area () { return this._width * this._height }
}
var r = new Rectangle(50, 20)
r.area === 1000
```

ECMAScript 5
```javascript
var Rectangle = function (width, height) {
    this._width  = width;
    this._height = height;
};
Rectangle.prototype = {
    set width  (width)  { this._width = width;               },
    get width  ()       { return this._width;                },
    set height (height) { this._height = height;             },
    get height ()       { return this._height;               },
    get area   ()       { return this._width * this._height; }
};
var r = new Rectangle(50, 20);
r.area === 1000;
```

