---
title: "Refactoring_legacy"
date: 2021-02-21
tags: []
draft: true
---

## Updating / refactoring legacy code

Updating legacy code generaly means transforming code to be
- more expressive
- more readable
- simpler

### Refactoring

#### Which parts requires our attention?
- frequent changes (needs to be easily understandable)
- code that doesn't work
- code that is presented to new employees
- places where new features are added

#### Boy scout rule
### Time for refactoring
Take a half of the time from estimation to improve the code
After that, if the code is better - do all the new way
If the code is worse - try to think why that happened, remember that, do the old way

### I Use compiler - it's you friend
- do not ignore warnings

### II Use correct C++ as a C++
- create small abstractions, small classes, tie variables and logic together under meaningful name
- cleanup & housekeeping - we have RAII
  - open / close file(s)
  - open / close connection(s)
  - acquire / release resources
  - (remember to make them non copyable)
-use polymorphism instead of raw `if / else` strucutres (eliminates the need to remember to add new branch in several places)
-`const` correctness (try making absolutely everything `const`)
-don't `typedef` structs! (it's so C-style)
- use C++ casting (easier to find, express intention)
  - `static_cast, dynamic_cast, reinterpret_cast, const_cast`
 
### III Use new C++ features

#### Use `auto`
Replace old, long declarations with auto (which must be initiailized)

#### Use `nullptr` 
Use `nulltpr` to avoid wrong overloads:
`f(NULL)` can be deducted as `f (int n)`  instead of  `f(int *n)`
using `nullptr` prevents such situations, it will never be casted to `int` !

#### Use lambdas
to replace funtion names which are not meaningful / informative:
`bool shouldCount(int i) { return i%7 == 0; }` should rather be replaced with lambda

#### Use range based for loops
Generally improves our code, makes it easier to read, protects us from pitfalls
potential pitfall: using the copy instead of the `real` obj.

#### Use  `=default`, `=delete` 
`=delete` instead of private constructors
`final` instead of private constructors
`=default` instead of empty, dummy ctor

#### Use enum classes 
Use `enum class` to avoid casting and value mismatch


### IV Use standard library

- `std:;string` instead of `const char*`
- use `std::vector` and `std::array` instead of C-style arrays
- RAII, rule of zero
- raw pointers are OK, BUT **owning** raw pointers are a NIGTHMARE
- prefer to pass arguments as (const) references, not as pointers
- when printing debug logs - create  `toString` method instead of logging to file / console directly. Then it'll be easier to change / disable logging

### V Refactoring / reorganizing code
- avoid **arrow code**:
  `if`
  ... `if`
  ... ... `if`    
  ... ... ...     proper action takes place here
  `else`
  it requires scrolling right
  if you need additional condition - problems 
change to 
`if`
 // report error
 `if`
 //return error
OK CODE
-avoid Wall of declarations , declare as close as possible, near the usage place, use `const`, use meaningful name
