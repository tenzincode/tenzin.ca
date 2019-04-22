---
title: programming paradigms
date: 2019-04-22 10:49:18
tags:
- javascript
- programming
---

![Programming Paradigms](/images/paradigms.png)

## Paradigms

### Imperative
Focuses on how to execute, defines control flow as statements that change a program state.  Main traits include direct assignments, common data structures, and global variables.  Example: C, C++, Java, PHP, Python, Ruby.

```java
int total = 0;
int number1 = 5;
int number2 = 10;
int number3 = 15;
total = number1 + number2 + number3;
```

Each statement changes the state of the program, from assigning values to each variable to the final addition of those values. Using a sequence of five statements the program is explicitly told how to add the numbers 5, 10 and 15 together.

### Declarative
Focuses on what to execute, defines program logic, but not detailed control flow.  Main traits include fourth-generation languages, spreadsheets, and report program generators.  Example: SQL, regular expressions, Prolog.

Declarative programs can be described as context independent. meaning they only declare what the ultimate goal is, not the intermediary steps to reach that goal.

```sql
WITH patient_data AS (
    SELECT patient_id, patient_name, hospital, drug_dosage
    FROM hospital_registry
    WHERE (last_visit &gt; now() - interval '14 days' OR last_visit IS NULL)
    AND city = "Los Angeles"
)

WITH average_dosage AS (
    SELECT hospital, AVG(drug_dosage) AS Average
    FROM patient_data
    GROUP BY hospital
)

SELECT count(hospital)
FROM average_dosage;
WHERE AVG(drug_dosage) &gt; 1000
```

### Procedural
Specifies the steps a program must take to reach a desired state, usually read in order from top to bottom.  Main traits include local variables, sequence, selection, iteration, and modularization.  Example: C, C++, PHP, Python.

```pascal
program Fibonacci;

function fib(n: Integer): Integer;
var a: Integer = 1;
    b: Integer = 1;
    f: Integer;
    i: Integer;
begin
  if (n = 1) or (n = 2) then
     fib := 1
  else
    begin
      for i := 3 to n do
      begin
         f := a + b;
         b := a;
         a := f;
      end;
      fib := f;
    end;
end;

begin
  WriteLn(fib(6));
end.
```

### Functional
Treats programs as evaluating mathematical functions and avoids state and mutable data.  Main traits include lambda calculus, compositionality, formula, recursion, referential transparency, no side effects.  Example: C++, Clojure, Elixir, Erlang, F#, Haskell, Kotlin, Lisp, Python, Ruby, Scala, Elm, Standard ML, JavaScript.

```javascript
// Non Pure Function
var y = 2
function adder (x){
  return x + y
}

// Pure Function
function add2 (x){
  return x + 2
}
```

### Object-Oriented
Organizes programs as objects, which are data structures consisting of datafields and methods together with their interactions.  Main traits include objects, methods, message passing, information hiding, data abstraction, encapsulation, polymorphism, inheritance, serialization-marshalling.  Example: C++, C#, Java, PHP, Python, Ruby, Scala, JavaScript.

```javascript
function Person(firstName, lastName) {
    // construct the object using the arguments
    this.firstName = firstName;
    this.lastName = lastName;

    // a method which returns the full name
    this.fullName = function() {
        return this.firstName + " " + this.lastName;
    }
}

var myPerson = new Person("John", "Smith");
console.log(myPerson.fullName());
```
