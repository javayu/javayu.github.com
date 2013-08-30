---
layout: post
title: "Stack implementation"
description: ""
category: 
tags: [Career, Coding]
---
{% include JB/setup %}
## Interface design:

* Create a class for the stack: `class Stack`
* Use template to adapt different data types: `<template typename T>`
* Constructor: `Stack()`
* Push: `Stack & push(T val)` , return `Stack &` to enable iterative push.
* Pop: `T pop()` , return the popped object
* Empty: `bool empty()` , return true iff stack has no element
* Optional functions: `size_t length()`, `void clear()`

For a class template, its declaration and implementation should better be put together in the same header file. This is because [the compiler need to see the implementation at instantiation](http://stackoverflow.com/questions/495021/why-can-templates-only-be-implemented-in-the-header-file).

{%highlight c++ linenos%}
#pragma once
using namespace std;

namespace CodeLib{
	template<typename T>
	class Stack{
	public:
		Stack();
		Stack & push(T val);
		T pop();
		bool empty();
		
		size_t length();
		void clear();
	}
}

{%endhighlight%}
##Array implementation

In the array implementation, I use a pointer `private T * vals` to point to dynamically assigned space and a `private size_t length` to record current stack top. The size of this space to set to a preset value. Using a pointer rather then a max-size array allows the stack to grow larger than preset max size. This pointer leads to a few other member and member functions:

1. `private size_t capacity` : size of currently assigned space
2. `private void doubleCapacity()` : strategy used when all space used by the stack. 
3. `~Stack()` and `Stack & operator=(const Stack & rhs)`: these are public functions involved in the copy control. Allocate new space and copy all members with assignment operator and copy constructor**(TODO)**. 


{%highlight c++ linenos%}
	public:
	~Stack();
	Stack & operator=(const Stack & rhs);
	Stack(const Stack & in);
	
	private:
		void doubleCapacity();

		T * vals;
		size_t capacity;
		size_t length;

{%endhighlight%}

### Notes
1. In `private void doubleCapacity()`, DE-allocate old space after copied to new space
2. In assignment operator, judge self-assign case **with pointer**
3. `underflow_error()` belongs to `namespace std` and `<stdexcept>`

###Code
See [here](https://github.com/javayu/JavaYuCodeBook/blob/master/CodeLib/CodeLib/JY_Stack.h) for code on github.