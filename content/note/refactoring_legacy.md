---
title: "Refactoring legacy code"
date: 2021-02-21
tags: []
draft: false
---

> Note: below you can find my jottings based on [Kate Gregory course](https://www.pluralsight.com/courses/cpp-updating-legacy-code)

Refactoring legacy code generally means transforming code to be:
- more expressive,
- more readable,
- simpler.

Which parts requires our highest attention?
- frequent changes (needs to be easily understandable),
- code that doesn't work (need to be fixed => changed),
- code that is presented to new employees,
- places where new features are added.

Boy scout rule: *Always leave the place cleaner that you've found it.*

#### How much time dedicate to refactoring?
Take a half of the time from estimation to improve the code
After that, if the code is better - do all the new way
If the code is worse - try to think why that happened, remember that, do the old way

### Refactoring process

#### I Use compiler - it's you friend
- do not ignore warnings

#### II Use correct C++ as a C++
- create small abstractions, small classes, 
- tie variables and logic together under meaningful name,
- cleanup & housekeeping - we have RAII
  - open / close file(s)
  - open / close connection(s)
  - acquire / release resources
  - (remember to make them non copyable)
- use polymorphism instead of raw `if / else` strucutres (eliminates the need to remember to add new branch in several places)
- `const` correctness (try making absolutely everything `const`)
- don't `typedef` structs! (it's so C-style)
- use C++ casting (easier to find, express intention)
  - `static_cast, dynamic_cast, reinterpret_cast, const_cast`
 
#### III Use new C++ features

- Use `auto` : replace old, long declarations with auto (also guarantees no unintialized variables)
- Use `nulltpr` to avoid wrong overloads:
	- `f(NULL)` can be deducted as `f (int n)`  instead of  `f(int *n)`.    
	Using `nullptr` prevents such situations, it will never be casted to `int` !

- Use *lambdas* to replace funtion names which are not meaningful / informative:    
	`bool shouldCount(int i) { return i%7 == 0; }`    
	should rather be replaced with lambda

- Use *range based for loops*: they generally improves our code, makes it easier to read, protects us from traps    
	potential pitfall: using the copy instead of the `real` obj.

- Use  `=default`, `=delete` 
	- `=delete` instead of private constructors
	- `final` instead of private constructors
	- `=default` instead of empty, dummy ctor

- Use `enum class` to avoid casting and value mismatch

#### IV Use standard library

- `std:;string` instead of `const char*`,
- use `std::vector` and `std::array` instead of C-style arrays,
- *RAII*, *rule of zero*,
- raw pointers are OK, BUT *owning* raw pointers are a *NIGTHMARE*,
- prefer to pass arguments as (`const)` references, not as pointers,
- when printing debug logs - create `toString()` method instead of logging to file / console directly. Then it'll be easier to change / disable logging.

#### V Reorganizing code
- avoid **arrow code**: 
   
``` cpp
if (condition1)
  if (condition2)
    if (condition3)
	  //proper action takes place here
else
```
Main issues:
- it requires scrolling right. 
- if you need additional condition that cause problems and complexity.

Try reorganizing it into: 

``` cpp
if (!condition1)
  //report error, return
if (!condition2) 
  //report error, return
if (!condition3) 
  //report error, return
//proper action takes place here
```

- Declaring variables
  - avoid **Wall of declarations** 
  - declare as close as possible, near the usage place, 
  - use `const`, 
  - use meaningful name
