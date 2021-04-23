---
title: "C++17"
date: 2021-04-18T19:29:08+02:00
draft: false
comments: false
comments: false
menu:
  docs:
    parent: "cpp"
images:
---

## Language elements 

### Nested namespaces
Instead of nesting the namespaces

	namespace A {
	  namespace B {
		namespace C {
		  // some code here
		}
	  }
	}


we can explicitly nest them

	namespace A::B::C {
	  // some code here
	}


### Variable declaration in control statements 

Ability to declare variable that is used inside the control statement body. Instead of:


	it = find(begin(names), end(names));
	if(it != end(names))
	  // some action on *it


we can simplify to:

	if(const auto it = find(begin(names), end(names)); it != end(names) 
	{
	  //some action on *it
	}
	else
	{
	  //*it available also here!
	}

### constexpr if

	if constexpr (condition) {
	}
	else {
	}


- Computed at compile time
- Can be leveraged when working with templates to check class properties / traits.
- Compiler will ignore the `else` part when `if` is already matched

### Structured bindings

`auto [var1, var2, var3] = GetSomeData();`

Example usages:
- Inserting element to map (much clearer than `.first`, `.second` approach)

		auto [pos, success] = dict.insert({"a", aVal});

- Browsing the map
	
		for (const auto& [key, val] : dict) {
		  std::cout << key << ":" << value << '\n';
		}
	
