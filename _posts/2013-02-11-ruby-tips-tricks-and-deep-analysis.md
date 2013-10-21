---
layout: post
title: "Ruby: tips, tricks and bytecode analysis"
description: "What is Ruby, syntactic details and how it works under the hood"
category: ruby
---
{% include JB/setup %}

#### My personal experience with Ruby

Basically I am not a Ruby developer: it means I never pushed to production any code written in Ruby but it doesn’t mean I don’t like to play with it. For now I wrote two programs in Ruby, two web applications (with the help of the excellent MVC framework Ruby on Rails) for university projects.

The source code of these two projects can be found on Github:
-	**LaFourchette2012** (<a href="https://github.com/ThibaultLaurens/La_Fourchette_2012" title="github.com/ThibaultLaurens/La_Fourchette_2012" target="_blank">source code</a>) written with four friends, this web application could be used to manage a restaurant’s chain (restaurants, customers & orders, waiters, cookers, payment, bill…). It has a nice “no-refresh” client interface written with **BackboneJS** and an admin panel.
-	**TraditOnRails** (<a href="https://github.com/ThibaultLaurens/TraditOnRails" title="github.com/ThibaultLaurens/TraditOnRails" target="_blank">source code</a>) a more recent project is a basic fictitious trading application with fictitious currencies stored in a **MongoDB** database. One characteristic of this project is that it uses a **Node.JS** server and shows real-time updates of currencies rate with the help of **socket.io** and a nice graph.

Oh and by the way, I should mention that this blog is written with **Jekyll** so once again: RUBY please!

So let’s first have a look at what is Ruby and at some details of the syntax that may surprise when you come from a C based programming language.


* * *

#### What is Ruby?

The ruby language is by definition **a minimalist and natural language** which is, I think, a very good thing for software development. It is also a **dynamic object oriented** language with **multiple programming paradigms** like imperative, functional or reflective. It was first written in C as a single-pass interpreted language and was designed for **programmer productivity and fun**.

* * *

#### What surprised me so far in Ruby
-	Single line comments are declared with a **#** and multi-line comments are declared with **=begin** and **=end** (this is strange right?)
-	Ruby doesn’t use curly brace to separate functions and set variable scope but use **indentation** (not exactly in the same way that the cousin Python) and the keyword **end** in the end of a code block
-	A variable will be automatically scoped into its block of code: classes and modules provide shelter from the global scope.
-	It uses **Bloks** to iterate on array like for example array.all?{|n| n > 4}, a.keep_if{|n| n>1}
-	It has multiple types for function such as Blocks and lambdas. Blocks is the most common way to use <a href="http://www.skorks.com/2010/05/closures-a-simple-explanation-using-ruby/" title="skorks.com/2010/05/closures-a-simple-explanation-using-ruby/" target="_blank"><strong>closure</strong></a> (concept from the Scheme language) in Ruby and is one of the most powerful feature of Ruby, it allows the developer to pass code to **iterators** such as each, detect or inject. (I should also say that Ruby extends the concept of block into a new one called <a href="http://www.ruby-doc.org/core-1.9.3/Proc.html" title="ruby-doc.org/core-1.9.3/Proc.html" target="_blank">Proc</a> which is **first class citizen** and can be simply created by adding Proc.new in a block.)

<script src="https://gist.github.com/ThibaultLaurens/4751477.js?file=block.rb"> </script>

Example of a Block in Rails: The code on the left side of the “do” will interact with the variable inside the two pipes on its right side.

* * *

#### How it works under the hood?

One thing the university teach me is that you can’t learn a new programming language if you don’t see how it works under the hood. So, let’s go a little deeper inside Ruby!

The actual version of Ruby, Ruby 1.9, uses **YARV** (<a href="http://en.wikipedia.org/wiki/YARV" title="wikipedia.org/wiki/YARV" target="_blank">Yet another Ruby VM</a>, also called **KRI**) which is a **bytecode interpreter**. YARV was designed to reduce the execution time of Ruby programs.
When a ruby program is executed, the first step will be to generate YARV instructions. In order to achieve this step, the code is first **tokenized**, then tokens are grouped into Ruby statements: it’s the **parsing**. Finally these statements are compiled into low level **YARV instructions**.
Ruby doesn’t use **Yacc** (<a href="http://en.wikipedia.org/wiki/Yacc" title="wikipedia.org/wiki/Yacc" target="_blank">Yet Another Compiler Compiler</a>) as **parser generator** but <a href="http://en.wikipedia.org/wiki/GNU_bison" title="wikipedia.org/wiki/GNU_bison" target="_blank">Bison</a>. The parser code is processed at the **build time** of Ruby and not at the run time of the program.
The second step is to run YARV instructions. YARV possess low level structures. It has for instance two instructions called **branchif** and **branchunless** (which can be understand has low level if and unless Ruby statement) and a low level function called **jump** in order to change the program counter and move into the compiled program.

For example, this if/else statement in Ruby…

<script src="https://gist.github.com/ThibaultLaurens/4751477.js?file=login.rb"> </script>

<br/>
(OK now hold your breath just a few seconds... and go!)

… corresponds to these YARV instructions:

<script src="https://gist.github.com/ThibaultLaurens/4751477.js?file=yarv.rb"> </script>

One thing or two about this code:

In line 0077, the getlocal instruction will leave a true or false value on the stack. Then, if the condition is false (does the user exist?), the branchunless line 0079 will jump to the line 131 where the else is defined (or should be defined, it looks like my Ruby console didn’t want me to see it!), to jump the if. If the condition is true, the code just after the brancheunless will be compiled and then a jump instruction line 0129 will jump to the end of the method to skip the else.

About the first line, it starts with **local table**. In fact, this is because Ruby encapsulates the execution of code by block. This series of bytecode instructions are specific to this if/else block. A block in Ruby is like a **closure**. It’s composed by a function to be executed (here the bytecode, internally it’s an **instruction sequence pointer**) and an environment which is a **dynamic frame pointer** (which points right to the **stack**).
In addition, a block can have an object environment. Internally Ruby represent a block using a **C structure** called **rb_block_t**.
YARV creates object with **RObject** structures which contains a **klass** pointer to the class and an **ivptr (instance variable pointer)** which points to an array of instance variable of the object.

* * *

#### The best is to see under a microscope

That’s all for now with Ruby but if you like this kind of things I may suggest you to read the **excellent book** of **Pat Shaughnessy**
<a href="http://patshaughnessy.net/ruby-under-a-microscope" title="patshaughnessy.net/ruby-under-a-microscope" target="_blank"><strong>Ruby Under a Microscope</strong></a>
<div><img alt="Ruby!" src="{{ ASSET_PATH }}/img/post/11-02-13-ruby/ruby.jpg" /></div>