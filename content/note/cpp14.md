---
title: "C++14"
date: 2021-04-18T19:29:06+02:00
draft: false
comments: false
images:
---

{{< table_of_contents >}}


## Language elements 

### tuples getters improvements 
ability to access element by type:

	std::get<double>(tuple)
	
> if more than 1 element of this type - compiler error

### Generic lambdas && init capture
Allow to use auto as parameter type 

	[](const auto& lhs, const auto& rhs) {
	   return lhs.length() < rhs.length();
	}


Ability to init variable when capturing to lambda

	[u{std::move(p)}] () { }

	[u = std::move(p)] () { }