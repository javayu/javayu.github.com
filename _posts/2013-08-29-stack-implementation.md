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

##Array implementation
In the array implementation, I use a pointer `private T * vals` to point to dynamically assigned space and a `private size_t length` to record current stack top. The size of this space to set to a preset value. Using a pointer rather then a max-size array allows the stack to grow larger than preset max size. This pointer leads to a few other member and member functions:

1. `private size_t capacity` : size of currently assigned space
2. `private void doubleCapacity()` : strategy used when all space used by the stack. 
3. `~Stack()` and `Stack & operator=(const Stack & rhs)`: these are public functions involved in the copy control. Allocate new space and copy all members with assignment operator and copy constructor**(TODO)**. 

### Notes
1. In `private void doubleCapacity()`, DE-allocate old space after copied to new space
2. In assignment operator, judge self-assign case **with pointer**
3. `underflow_error()` belongs to `namespace std` and `<stdexcept>`

###Code
Implemented in Visual Studio 2012

{%highlight c++ linenos%}
#pragma once
using namespace std;

namespace CodeLib{
	template<typename T>
	class Stack{
	public:
		friend class StackTest;

		Stack(size_t maxSize = 500)
		{
			capacity = maxSize;
			vals = new T[capacity];
			length = 0;
		}

		~Stack(){ 
			if(vals != 0) 
				delete[] vals;
		}

		Stack & operator=(const Stack & rhs)
		{
			if( this != &rhs)
			{
				capacity = rhs.capacity;
				length =  rhs.length;
				if(vals != 0)
					delete[] vals;
				vals = new T[capacity];
				for( size_t i=0; i<length; i++)
					vals[i] = rhs.vals[i];
			}
			return *this;
		}

		T pop()
		{
			if(empty())
				throw underflow_error("Stack is empty");
			return vals[--length];
		}

		Stack & push(T val)
		{
			if(length >= capacity)
				doubleCapacity();
			vals[length++] = val;
			return *this;
		}

		inline bool empty() { return length <= 0; }

		inline void clear() { length = 0;	}

	private:
		void doubleCapacity()
		{
			capacity *= 2;
			T * newVals = new T[capacity];
			for(size_t i=0; i<length; i++)
				newVals[i] = vals[i];
			delete[] vals;
			vals = newVals;
		}

		T * vals;
		size_t capacity;
		size_t length;
	};
}

{%endhighlight%}
