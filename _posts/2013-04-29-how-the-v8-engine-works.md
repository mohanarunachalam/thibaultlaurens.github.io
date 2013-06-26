---
layout: post
title: "How the V8 engine works?"
description: "How it works and how to write performant JavaScript code for the engine"
category: javascript
tags: 
- blog-post
---
{% include JB/setup %}

#### What is V8?
V8 is a **JavaScript engine** build in the google development center in Germany. It is 
<a href="https://code.google.com/p/v8/wiki/Source" title="code.google.com/p/v8/wiki/Source" target="_blank"><strong>open source</strong></a> and written in **C++**. It is used for both client side (Googgle Chrome) and server side (node.js) JavaScript applications. 

V8 was first designed to increase the performance of the JavaScript execution inside web browsers. In order to obtain speed, V8 translates JavaScript code into more efficient machine code instead of using an interpreter. It compiles JavaScript code into machine code at execution by implementing a **JIT (Just-In-Time) compiler** like a lot of modern JavaScript engines such as SpiderMonkey or Rhino (Mozilla) are doing. The main difference with V8 is that it doesn't produce bytecode or any intermediate code.

The aim of this article is to show and to understand **how V8 works, in order to produce optimized code** for both client side or server side applications. If you are already asking yourself "Should I care about JavaScript performance?" then I will answer with a citation, from Daniel Clifford (tech lead and manager of the V8 team): "It's not just about making your current application run faster, it's about enabling things that you have never been able to do in the past".

<div class="six centered columns">
    <img alt="V8!" src="{{ ASSET_PATH }}foundation/images/post/21-03-13-v8/v8.PNG"/>
</div>


#### Hidden class
JavaScript is a protoptye-based language: there is **no classes** and objects are created by using a cloning process. Also, JavaScript is dynamically typed: types and type informations are not explicit and properties can be added and deleted to objects on the fly. Accessing types and properties effectively makes a first big challenge for V8. Instead of using a dictionnary-like data structure for storing object properties and doing a dynamic lookup to resolve the property location (like most JavaScript engine do), V8 creates **hidden classes**, at runtime, in order to have an internal representation of the type system and to improve the property access time.

Let's have for instance a "Point" function and the creation of two "Point" objects:

<div class="ten centered columns">
    <img alt="hidden class" src="{{ ASSET_PATH }}foundation/images/post/21-03-13-v8/hiddenclass.PNG"/>
</div>

If the layout are the same, which is the case here, p and q belong to the same hidden class created by V8. This highlight another advantage in using hidden classes: it allows V8 to group objects witch properties are the same. Here "p" and "q" use the same **optimized code**.

Now let's assume that we want to add a "z" property to our "q" object, right after its declaration (which is perfectly fine with a dynamically typed language). 

How V8 will deal with this scenario? 
In fact, V8 **creates a new hidden class everytime the constructor function declares a property** and keeps track of the changement in the hidden class. Why? Because if two objects are created ("p" and "q") and if a member is added to the second object ("q") after the creation, V8 needs to keep the last hidden class created (for the first object "p") and to create a new one (for the second object "q") with the new member.

<div class="ten centered columns">
    <img alt="transition information" src="{{ ASSET_PATH }}foundation/images/post/21-03-13-v8/transition.PNG"/>
</div>

Everytime a new hidden class is created, the previous one is updated with a class transition indicating what hidden class has to be used instead of it.

##### Code optimization
Because V8 creates a new hidden class for each property, hidden class creation should be kept to a minimum. To do this, try to avoid to add properties after the object creation and always initialize object members in the same order (to avoid the creation of different three of hidden classes).

\[Update\] Another trick:
Monomorphic operations are operations which only work on objects with the same hidden class.
V8 creates hidden class when we call a function, if we call it again with different parameter types, V8 need to create another hidden class: **Prefer monomorphic code to polymorphic code**

<br/>

***

<br/>

#### More example on how V8 optimized JavaScript code

<br/>

##### Tagged values

To have an efficient representation of numbers and JavaScript objects, V8 represents both of us with a **32 bits** value. It uses a bit to know if it is an object (flag = 1) or an integer (flag = 0) called here SMall Integer or **SMI** because of its 31 bits. Then, if a numeric value is bigger than 31 bits, V8 will box the number, turning it to a double and creating a new object to put the number inside.

**Code optimization**: Try to use 31 bit signed numbers whenever possible to avoid the expensive boxing operation into JavaScript object.

<br/>

##### Arrays
V8 uses to differents methods to handle arrays:
* **Fast elements**: Designed for arrays where set of keys are very compact. They have a **linear storage buffer** that can be accessed very efficiently.
* **Dictionary elements**: Designed for sparse arrays which don't have every elements inside of them. It is actually a **hash table**, more expensive to access than "Fast Elements"

**Code optimization**: Be sure that V8 uses "Fast Elements" to handle arrays, in other words, avoid sparse arrays where keys are not next incremental numbers. Also, try to avoid to pre-allocate large arrays, better is to grow as you go. Finally, don't delete elements in arrays: it makes the key set sparse.

<br/>

***

<br/>

#### How V8 compiles JavaScript code?

<br/>

V8 has two compilers! 

- A **"Full" Compiler** that can generate good code for any JavaScript: good but not great JIT code. The goal of this compiler is to generates code quickly. To achieve its goal, it doesn't do any type analysis and doesn"t know anything about types. Instead, it uses an **Inline Caches** or "IC" strategy to refine knowledge about types while the program run. IC is very efficient and bring about 20 times speed improvment.

- An **Optimizing Compiler** that produces great code for most of the JavaScript language. It comes later and re-compiles hot functions. The optimizing compiler takes types from the Inline Cache and make decisions about how to optimize the code better. However, some language features are not supported yet like try/catch block for instance. (The workaround for try/catch blocks is to write the "non stable" code into a function and to call the function in the try block)

<br/>

**Code optimization**: V8 also supports **de-optimization**: the optimizing compiler makes optimistic assumptions from the Inline Cache about the different types, de-optimization comes if these assumptions are invalids. For example, if an hidden class generated was not the one expected, V8 throws away the optimized code and come back to the Full Compiler to get types again from the Inline Cache. This process is slow and should be avoided by trying to not change functions after they are optimized.

<br/>

***

<br/>

#### Resources

- Google I/O 2012 "Breaking the JavaScript Speed Limit with V8" with Daniel Clifford, tech lead and manager of the V8 team: 
<a href="https://www.youtube.com/watch?v=UJPdhx5zTaw" title="video" target="_blank">video</a> and 
<a href="http://v8-io12.appspot.com" title="slides" target="_blank">slides</a>.
- V8: an open source JavaScript engine: 
<a href="http://www.youtube.com/watch?v=hWhMKalEicY" title="video" target="_blank">video</a> of Lars Bak, V8 core engineer.
- Nikkei Electronics Asia blog post: <a href="http://techon.nikkeibp.co.jp/article/HONSHI/20090106/163615/" title="go to techon.nikkeibp.co.jp" target="_blank">Why Is the New Google V8 Engine So Fast?</a>