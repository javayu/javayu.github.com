---
layout: post
title: "Convert a Visual Studio 2010 project to a Linux project"
description: ""
category: Coding 
tags: [Coding, Linux, Visual studio]
---
{% include JB/setup %}

##In short
The major steps are:

1.	Remove stdafx.h and improve dependency
1.	Modify signature for "main" function
1.	Setup Makefile in Linux system.
1.	<s>Substitute all "pragma once"(optional)</s>
2.	Modify implementation details
	
##Modify signature for "main" function
Modify the main function name from "int _tmain(int argc, _TCHAR* argv[])" to "int main()". "_tmain" is not  well recognized by Linux compilers like g++.

## Remove stdafx.h and improve dependency
For Visual Studio projects, stdafx.h is used as a 'precompiled header' for projects with Windows dependencies. Despite that its existence does not hinder Linux compilation, it's not a common file for the Linux system. See [here](http://stackoverflow.com/questions/4726155/whats-the-use-for-stdafx-h-in-visual-studio) for more details.

To remove this file, first check the project's properties. Right click on project name->Properties->Configuration properties->C/C++ -> Precompiled Headers. Set the value of 'Precompiled header' to 'Not Using Precompiled Headers' 

Next step is to remove dependencies on 'stdafx.h' and 'stdafx.cpp' . Organize header files according to [Google Style Guide](http://google-styleguide.googlecode.com/svn/trunk/cppguide.xml#Header_Files) and [here](http://stackoverflow.com/questions/346058/c-class-header-files-organization).

## Substitute 'pragma once'
<s>'pragma once' is a macro unique to Visual Studio. In Linux or other platforms including Windows, the #ifndef macro is a more widely used standard approach.</s> 
The syntax for #ifndef is:

{%highlight c++ linenos%}
#ifndef _HEADER_NAME_H
#define _HEADER_NAME_H
..
{Your code here}
..
#endif
{%endhighlight%}

<s>This approach is also accepted by Visual Studio.</s>

According to [wikipedia](http://en.wikipedia.org/wiki/Pragma_once): 
>In the C and C++ programming languages, #pragma once is a non-standard but widely supported preprocessor directive designed to cause the current source file to be included only once in a single compilation. Thus, #pragma once serves the same purpose as #include guards, but with several advantages, including: less code, avoidance of name clashes, and sometimes improved compile speed.

Considering 'pragma once' is widely supported, whether to substitute or not becomes more [a problem of personal taste](http://stackoverflow.com/questions/1143936/pragma-once-vs-include-guards).  

## Modify implementation details
1. nullptr is not supported by g++ unless C++0x support is specified. See http://stackoverflow.com/questions/10033373/c-error-nullptr-was-not-declared-in-this-scope-in-eclipse-ide

2. sprintf_s is none-standard function and not supported by g++. [Reference](http://stackoverflow.com/questions/4828228/sprintf-s-was-not-declared-in-this-scope) Use "snprintf" instead.  Example: 
`snprintf(charArrayAgain, 2, "%d", number);`from [here](http://stackoverflow.com/questions/7505500/snprintf-and-sprintf-explanation) 

3.  most important: g++ is more strict on standards. Following stuff are not accepted by g++ by passes Visual Studio compilation with warning:

a.	Implicit conversion from a non-const object to a const object.

b.	Giving an rvalue as input where a non-const reference is required. This is similar to a. For a and b, see [here](http://stackoverflow.com/questions/445570/when-cant-an-object-be-converted-to-a-reference) for reference.

c.	multiple layer of template:  `List<List<edge>>`,  the `>>` is not supported by g++ unless c++ 0x support specified.

d.	Conversion between `string` and `char[]` . Some implicit conversion is supported by Microsoft but g++ holds tight on it.
4.  The meaning for `-Wreorder` of g++: [here](http://stackoverflow.com/questions/1828037/whats-the-point-of-g-wreorder)