---
title: ecmascript 6 features - part 2
date: 2021-04-21 17:30:19
tags:
 - javascript
 - programming
---

Continued from [Part 1](/2021/04/20/ecmascript6-1/)

A brief overview of some of the latest features available in ECMAScript 6, with examples of how they were written previously in ECMAScript 5, if even possible.

## Symbol Type

### Symbol Type
Unique and immutable data type to be used as an identifier for object properties.  Symbol can have an optional description, but only for debugging purposes.

ECMAScript 6
```javascript
Symbol("foo") !== Symbol("foo")
const foo = Symbol()
const bar = Symbol()
typeof foo === "symbol"
typeof bar === "symbol"
let obj = {}
obj[foo] = "foo"
obj[bar] = "bar"
JSON.stringify(obj) // {}
Object.keys(obj) // []
Object.getOwnPropertyNames(obj) // []
Object.getOwnPropertySymbols(obj) // [ foo, bar ]
```

ECMAScript 5
```javascript
// no equivalent in ES5
```

### Global Symbols
Global symbols indexed through unique keys.

ECMAScript 6
```javascript
Symbol.for("app.foo") === Symbol.for("app.foo")
const foo = Symbol.for("app.foo")
const bar = Symbol.for("app.bar")
Symbol.keyFor(foo) === "app.foo"
Symbol.keyFor(bar) === "app.bar"
typeof foo === "symbol"
typeof bar === "symbol"
let obj = {}
obj[foo] = "foo"
obj[bar] = "bar"
JSON.stringify(obj) // {}
Object.keys(obj) // []
Object.getOwnPropertyNames(obj) // []
Object.getOwnPropertySymbols(obj) // [ foo, bar ]
```

ECMAScript 5
```javascript
// no equivalent in ES5
```

## Iterators

### Iterator & For-Of Operator
Support "interable" protocol to allow objects to customize their iteration behaviour.  Additionally, support "iterator" protocol to produce sequence of values, either finite or infinite.  Finally, provide convenient of-operator to iterate over all values of an iterable object.

ECMAScript 6
```javascript
let fibonacci = {
    [Symbol.iterator]() {
        let pre = 0, cur = 1
        return {
            next () {
                [ pre, cur ] = [ cur, pre + cur ]
                return { done: false, value: cur }
            }
        }
    }
}

for (let n of fibonacci) {
    if (n > 1000)
        break
    console.log(n)
}
```

ECMAScript 5
```javascript
var fibonacci = {
    next: (function () {
        var pre = 0, cur = 1;
        return function () {
            tmp = pre;
            pre = cur;
            cur += tmp;
            return cur;
        };
    })()
};

var n;
for (;;) {
    n = fibonacci.next();
    if (n > 1000)
        break;
    console.log(n);
}
```

## Generators

### Generator Function, Iterator Protocol
Support for generators, a special case of iterators containing a generator function, where the control flow can be paused and resumed, in order to produce a sequence of values (either finite or infinite).

ECMAScript 6
```javascript
let fibonacci = {
    *[Symbol.iterator]() {
        let pre = 0, cur = 1
        for (;;) {
            [ pre, cur ] = [ cur, pre + cur ]
            yield cur
        }
    }
}

for (let n of fibonacci) {
    if (n > 1000)
        break
    console.log(n)
}
```

ECMAScript 5
```javascript
var fibonacci = {
    next: (function () {
        var pre = 0, cur = 1;
        return function () {
            tmp = pre;
            pre = cur;
            cur += tmp;
            return cur;
        };
    })()
};

var n;
for (;;) {
    n = fibonacci.next();
    if (n > 1000)
        break;
    console.log(n);
}
```

### Generator Function, Direct Use
Support for generator functions, a special variant of functions, where the control flow can be paused and resumed in order to produce a sequence of values (either finite or infinite).

ECMAScript 6
```javascript
function* range (start, end, step) {
    while (start < end) {
        yield start
        start += step
    }
}

for (let i of range(0, 10, 2)) {
    console.log(i) // 0, 2, 4, 6, 8
}
```

ECMAScript 5
```javascript
function range (start, end, step) {
    var list = [];
    while (start < end) {
        list.push(start);
        start += step;
    }
    return list;
}

var r = range(0, 10, 2);
for (var i = 0; i < r.length; i++) {
    console.log(r[i]); // 0, 2, 4, 6, 8
}
```

### Generator Matching
Generator functions can produce and spread a sequence of values (either finite or infinite).

ECMAScript 6
```javascript
let fibonacci = function* (numbers) {
    let pre = 0, cur = 1
    while (numbers-- > 0) {
        [ pre, cur ] = [ cur, pre + cur ]
        yield cur
    }
}

for (let n of fibonacci(1000))
    console.log(n)

let numbers = [ ...fibonacci(1000) ]
let [ n1, n2, n3, ...others ] = fibonacci(1000)
```

ECMAScript 5
```javascript
// no equivalent in ES5
```

### Generator Control-Flow
Generator functions can support asynchronous programming in the style of "co-routines" in combination with promises.

ECMAScript 6
```javascript
// generic asynchonous control-flow driver
function async (proc, ...params) {
    let iterator = proc(...params)
    return new Promise((resolve, reject) => {
        let loop = (value) => {
            let result
            try {
                result = iterator.next(value)
            }
            catch (err) {
                reject(err)
            }
            if (result.done)
                resolve(result.value)
            else if (typeof result.value === "object" && typeof result.value.then === "function")
                result.value.then((value) => {
                    loop(value)
                }, (err) => {
                    reject(err)
                })
            else
                loop(result.value)
        }
        loop()
    })
}

// application-specific asynchronous builder
function makeAsync (text, after) {
    return new Promise((resolve, reject) => {
        setTimeout(() => resolve(text), after)
    })
}

// application-specific asynchronous procedure
async(function* (greeting) {
    let foo = yield makeAsync("foo", 300)
    let bar = yield makeAsync("bar", 200)
    let baz = yield makeAsync("baz", 100)
    return `${greeting} ${foo} ${bar} ${baz}`
}, "Hello").then((msg) => {
    console.log("RESULT:", msg) // "Hello foo bar baz"
})
```

ECMAScript 5
```javascript
// no equivalent in ES5
```

### Generator Methods
Support for methods in classes and on objects, based on generator functions

ECMAScript 6
```javascript
class Clz {
    * bar () {
        ...
    }
}
let Obj = {
    * foo () {
        ...
    }
}
```

ECMAScript 5
```javascript
// no equivalent in ES5
```

## Map/Set & WeakMap/WeakSet

### Set Data-Structure
Cleaner data-structure for common algorithms based on sets.

ECMAScript 6
```javascript
let s = new Set()
s.add("hello").add("goodbye").add("hello")
s.size === 3
s.has("hello") === true
for (let key of s.values()) // insertion order
    console.log(key)
```

ECMAScript 5
```javascript
var s = {};
s["hello"] = true; s["goodbye"] = true; s["hello"] = true;
Object.keys(s).length === 2
s["hello"] === true;
for (var key in s) // arbitrary order
    if (s.hasOwnProperty(key))
        console.log(s[key]);
```

### Map Data-Structure
Cleaner data-structure for common algorithms based on maps.

ECMAScript 6
```javascript
let m = new Map()
let s = Symbol()
m.set("hello", 42)
m.set(s, 34)
m.get(s) === 34
m.size === 2
for (let [ key, val ] of m.entries())
    console.log(key + " = " + val)
```

ECMAScript 5
```javascript
var m = {};
// no equivalent in ES5
m["hello"] = 42;
// no equivalent in ES5
// no equivalent in ES5
Objects.keys(m).length === 2;
for (key in m) {
    if (m.hasOwnProperty(key)) {
        var val = m[key];
        console.log(key + " = " + val);
    }
}
```

### Weak-Link Data-Structures
Memory-leak-free Object-key'd side-by-side data-structures.

ECMAScript 6
```javascript
let isMarked = new WeakSet()
let attachedData = new WeakMap()

export class Node {
    constructor () { this.id = id }
    mark () { isMarked.add(this) }
    unmark () { isMarked.delete(this) }
    marked () { return isMarked.has(this) }
    set data (data) { attachedData.set(this, data) }
    get data () { return attachedData.get(this) }
}

let foo = new Node("foo")

JSON.stringify(foo) === '{ "id": "foo" }'
foo.mark()
foo.data = "bar"
foo.data === "bar"
JSON.stringify(foo) === '{ "id": "foo" }'

isMarked.has(foo) === true
attachedData.has(foo) === true
foo = null /* remove only reference to foo */
attachedData.has(foo) === false
isMarked.has(foo) === false
```

ECMAScript 5
```javascript
// no equivalent in ES5
```

## Typed Arrays

### Typed Arrays
Support for arbitrary byte-based data structures to implement network protocols, cryptography algorithms, file format manipulations, etc.

ECMAScript 6
```javascript
// ES6 class equivalent to the following C structure:
// struct Example { unsigned long id; char username[16]; float amountDue }
class Example {
    constructor (buffer = new ArrayBuffer(24)) {
        this.buffer = buffer
    }
    set buffer (buffer) {
        this._buffer = buffer
        this._id = new Uint32Array (this._buffer, 0, 1)
        this._username = new Uint8Array (this._buffer, 4, 16)
        this._amountDue = new Float32Array(this._buffer, 20, 1)
    }
    get buffer () { return this._buffer }
    set id (v) { this._id[0] = v }
    get id () { return this._id[0] }
    set username (v) { this._username[0] = v }
    get username () { return this._username[0] }
    set amountDue (v) { this._amountDue[0] = v }
    get amountDue () { return this._amountDue[0] }
}

let example = new Example()
example.id = 7
example.username = "John Doe"
example.amountDue = 42.0
```

ECMAScript 5
```javascript
// no equivalent in ES5
// (only an equivalent in HTML5)
```

## New Build-In Methods

### Object Property Assignment
New function for assigning enumerable properties of one or more source objects onto a destination object.

ECMAScript 6
```javascript
var dest = { quux: 0 }
var src1 = { foo: 1, bar: 2 }
var src2 = { foo: 3, baz: 4 }
Object.assign(dest, src1, src2)
dest.quuz === 0
dest.foo === 3
dest.bar === 2
dest.baz === 4
```

ECMAScript 5
```javascript
var dest = { quux: 0 };
var src1 = { foo: 1, bar: 2 };
var src2 = { foo: 3, baz: 4 };
Object.keys(src1).forEach(function(k) {
    dest[k] = src1[k];
});
Object.keys(src2).forEach(function(k) {
    dest[k] = src2[k];
});
dest.quux === 0;
dest.foo  === 3;
dest.bar  === 2;
dest.baz  === 4;
```

### Array Element Finding
New function for finding an element in an array.

ECMAScript 6
```javascript
[ 1, 3, 4, 2 ].find(x => x > 3) // 4
[ 1, 3, 4, 2 ].findIndex(x => x > 3) // 2
```

ECMAScript 5
```javascript
[ 1, 3, 4, 2 ].filter(function (x) { return x > 3 })[0]; // 4
// no equivalent in ES5
```

### String Repeating
New string repeating functionality.

ECMAScript 6
```javascript
" ".repeat(4 * depth)
"foo".repeat(3)
```

ECMAScript 5
```javascript
Array((4 * depth) + 1).join(" ");
Array(3 + 1).join("foo");
```

### String Searching
New specific string functions to search for a sub-string.

ECMAScript 6
```javascript
"hello".startsWith("ello", 1) // true
"hello".endsWith("hell", 4) // true
"hello".includes("ell") // true
"hello".includes("ell", 1) // true
"hello".includes("ell", 2) // false
```

ECMAScript 5
```javascript
"hello".indexOf("ello") === 1; // true
"hello".indexOf("hell") === (4 - "hell".length); // true
"hello".indexOf("ell") !== -1; // true
"hello".indexOf("ell", 1) !== -1; // true
"hello".indexOf("ell", 2) !== -1; // false
```

### Number Type Checking
New functions for checking for non-numbers and finite numbers.

ECMAScript 6
```javascript
Number.isNaN(42) === false
Number.isNaN(NaN) === true

Number.isFinite(Infinity) === false
Number.isFinite(-Infinity) === false
Number.isFinite(NaN) === false
Number.isFinite(123) === true
```

ECMAScript 5
```javascript
var isNaN = function (n) {
    return n !== n;
};
var isFinite = function (v) {
    return (typeof v === "number" && !isNaN(v) && v !== Infinity && v !== -Infinity);
};
isNaN(42) === false;
isNaN(NaN) === true;

isFinite(Infinity) === false;
isFinite(-Infinity) === false;
isFinite(NaN) === false;
isFinite(123) === true;
```

### Number Safety Checking
Checking whether an integer number is in the safe range, ie: it is correctly represented by JavaScript (where all numbers, including integers, are technically floating point numbers).

ECMAScript 6
```javascript
Number.isSafeInteger(42) === true
Number.isSafeInteger(90578921689765987123) === false
```

ECMAScript 5
```javascript
function isSafeInteger (n) {
    return (
           typeof n === 'number'
        && Math.round(n) === n
        && -(Math.pow(2, 53) - 1) <= n
        && n <= (Math.pow(2, 53) - 1)
    );
}
isSafeInteger(42) === true;
isSafeInteger(9007199254740992) === false;
```

### Number Comparison
Availability of a standard Epsilon value for more precise comparison of floating point numbers.

ECMAScript 6
```javascript
console.log(0.1 + 0.2 === 0.3) // false
console.log(Math.abs((0.1 + 0.2) - 0.3) < Number.EPSILON) // true
```

ECMAScript 5
```javascript
console.log(0.1 + 0.2 === 0.3); // false
console.log(Math.abs((0.1 + 0.2) - 0.3) < 2.220446049250313e-16); // true
```

### Number Truncation
Truncate a floating point number to its integral part, completely dropping the fractional part.

ECMAScript 6
```javascript
console.log(Math.trunc(42.7)) // 42
console.log(Math.trunc(0.1)) // 0
console.log(Math.trunc(-0.1)) // -0
```

ECMAScript 5
```javascript
function mathTrunc (x) {
    return (x < 0 ? Math.ceil(x) : Math.floor(x));
}
console.log(mathTrunc(42.7)) // 42
console.log(mathTrunc( 0.1)) // 0
console.log(mathTrunc(-0.1)) // -0
```

### Number Sign Determination
Determine the sign of a number, including special cases of signed zero and non-number.

ECMAScript 6
```javascript
console.log(Math.sign(7))   // 1
console.log(Math.sign(0))   // 0
console.log(Math.sign(-0))  // -0
console.log(Math.sign(-7))  // -1
console.log(Math.sign(NaN)) // NaN
```

ECMAScript 5
```javascript
function mathSign (x) {
    return ((x === 0 || isNaN(x)) ? x : (x > 0 ? 1 : -1));
}
console.log(mathSign(7))   // 1
console.log(mathSign(0))   // 0
console.log(mathSign(-0))  // -0
console.log(mathSign(-7))  // -1
console.log(mathSign(NaN)) // NaN
```

## Promises

### Promise Usage
First class representation of a value that may be made asynchronously and be available in the future.

ECMAScript 6
```javascript
function msgAfterTimeout (msg, who, timeout) {
    return new Promise((resolve, reject) => {
        setTimeout(() => resolve(`${msg} Hello ${who}!`), timeout)
    })
}
msgAfterTimeout("", "Foo", 100).then((msg) =>
    msgAfterTimeout(msg, "Bar", 200)
).then((msg) => {
    console.log(`done after 300ms:${msg}`)
})
```

ECMAScript 5
```javascript
function msgAfterTimeout (msg, who, timeout, onDone) {
    setTimeout(function () {
        onDone(msg + " Hello " + who + "!");
    }, timeout);
}
msgAfterTimeout("", "Foo", 100, function (msg) {
    msgAfterTimeout(msg, "Bar", 200, function (msg) {
        console.log("done after 300ms:" + msg);
    });
});
```

### Promise Combination
Combine one or more promises into new promises without having to take care of ordering of the underlying asynchronous operations yourself.

ECMAScript 6
```javascript
function fetchAsync (url, timeout, onData, onError) {
    ...
}
let fetchPromised = (url, timeout) => {
    return new Promise((resolve, reject) => {
        fetchAsync(url, timeout, resolve, reject)
    })
}
Promise.all([
    fetchPromised("http://backend/foo.txt", 500),
    fetchPromised("http://backend/bar.txt", 500),
    fetchPromised("http://backend/baz.txt", 500)
]).then((data) => {
    let [ foo, bar, baz ] = data
    console.log(`success: foo=${foo} bar=${bar} baz=${baz}`)
}, (err) => {
    console.log(`error: ${err}`)
})
```

ECMAScript 5
```javascript
function fetchAsync (url, timeout, onData, onError) {
    …
}
function fetchAll (request, onData, onError) {
    var result = [], results = 0;
    for (var i = 0; i < request.length; i++) {
        result[i] = null;
        (function (i) {
            fetchAsync(request[i].url, request[i].timeout, function (data) {
                result[i] = data;
                if (++results === request.length)
                    onData(result);
            }, onError);
        })(i);
    }
}
fetchAll([
    { url: "http://backend/foo.txt", timeout: 500 },
    { url: "http://backend/bar.txt", timeout: 500 },
    { url: "http://backend/baz.txt", timeout: 500 }
], function (data) {
    var foo = data[0], bar = data[1], baz = data[2];
    console.log("success: foo=" + foo + " bar=" + bar + " baz=" + baz);
}, function (err) {
    console.log("error: " + err);
});
```

## Meta-Programming

### Proxying
Hooking into runtime-level object meta-operations.

ECMAScript 6
```javascript
let target = {
    foo: "Welcome, foo"
}
let proxy = new Proxy(target, {
    get (receiver, name) {
        return name in receiver ? receiver[name] : `Hello, ${name}`
    }
})
proxy.foo === "Welcome, foo"
proxy.world === "Hello, world"
```

ECMAScript 5
```javascript
// no equivalent in ES5
```

### Reflection
Make calls corresponding to the object meta-operations.

ECMAScript 6
```javascript
let obj = { a: 1 }
Object.defineProperty(obj, "b", { value: 2 })
obj[Symbol("c")] = 3
Reflect.ownKeys(obj) // [ "a", "b", Symbol(c) ]
```

ECMAScript 5
```javascript
var obj = { a: 1 };
Object.defineProperty(obj, "b", { value: 2 });
// no equivalent in ES5
Object.getOwnPropertyNames(obj); // [ "a", "b" ]
```

## Internationalization & Localization

### Collation
Sorting a set of strings and searching within a set of strings.  Collation is parameterized by locale and aware of Unicode.

ECMAScript 6
```javascript
// in German,  "ä" sorts with "a"
// in Swedish, "ä" sorts after "z"
var list = [ "ä", "a", "z" ]
var l10nDE = new Intl.Collator("de")
var l10nSV = new Intl.Collator("sv")
l10nDE.compare("ä", "z") === -1
l10nSV.compare("ä", "z") === +1
console.log(list.sort(l10nDE.compare)) // [ "a", "ä", "z" ]
console.log(list.sort(l10nSV.compare)) // [ "a", "z", "ä" ]
```

ECMAScript 5
```javascript
// no equivalent in ES5
```

### Number Formatting
Format numbers with digit grouping and localized separators.

ECMAScript 6
```javascript
var l10nEN = new Intl.NumberFormat("en-US")
var l10nDE = new Intl.NumberFormat("de-DE")
l10nEN.format(1234567.89) === "1,234,567.89"
l10nDE.format(1234567.89) === "1.234.567,89"
```

ECMAScript 5
```javascript
// no equivalent in ES5
```

### Currency Formatting
Format numbers with digit grouping, localized separators, and attached currency symbol.

ECMAScript 6
```javascript
var l10nUSD = new Intl.NumberFormat("en-US", { style: "currency", currency: "USD" })
var l10nGBP = new Intl.NumberFormat("en-GB", { style: "currency", currency: "GBP" })
var l10nEUR = new Intl.NumberFormat("de-DE", { style: "currency", currency: "EUR" })
l10nUSD.format(100200300.40) === "$100,200,300.40"
l10nGBP.format(100200300.40) === "£100,200,300.40"
l10nEUR.format(100200300.40) === "100.200.300,40 €"
```

ECMAScript 5
```javascript
// no equivalent in ES5
```

### Date/Time Formatting
Format date/time with localized ordering and separators.

ECMAScript 6:
```javascript
var l10nEN = new Intl.DateTimeFormat("en-US")
var l10nDE = new Intl.DateTimeFormat("de-DE")
l10nEN.format(new Date("2015-01-02")) === "1/2/2015"
l10nDE.format(new Date("2015-01-02")) === "2.1.2015"
```

ECMAScript 5:
```javascript
// no equivalent in ES5
```
