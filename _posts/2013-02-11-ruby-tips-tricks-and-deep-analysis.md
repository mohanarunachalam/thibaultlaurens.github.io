---
layout: post
title: "Ruby: tips, tricks and bytecode analysis"
description: "syntactic details and how it works under the hood"
category: ruby
---
{% include JB/setup %}


Ruby is by definition a minimalist and natural language which, I think, is a very good thing for software development. It is also a dynamic, object oriented language but can also be used with multiple programming paradigms like imperative, functional or reflective. It was first written in C, as a single-pass interpreted language and was designed for "programmer productivity and fun".

* * *

#### What surprised me so far

Coming from a C based programming language, some part of the ruby syntax was a bit surprising at first. I have listed a few things below:

- Single line comments are declared with a `#` and multi-line comments are declared with `=begin` and `=end`.
- Ruby doesn’t use curly braces to separate functions and set variable scope, it uses indentation instead, but not exactly in the same way as in Python. It also has the keyword `end` to use at the end of every code block.
- A variable will be automatically scoped into its block of code: classes and modules provide shelter from the global scope.
- It uses `Blocks` to iterate on array like for example `array.all?{|n| n > 4}`, `a.keep_if{|n| n>1}`
- It has multiple types for functions such as blocks and lambdas. Blocks is the most common way to use <a href="http://www.skorks.com/2010/05/closures-a-simple-explanation-using-ruby/" title="skorks.com/2010/05/closures-a-simple-explanation-using-ruby/" target="_blank"><strong>closures</strong></a> (a concept from the Scheme language) and is one of the most powerful feature of Ruby. It allows the developer to pass code to iterators such as `each`, `detect` or `inject`. (I should also say that Ruby extends the concept of blocks into a new one called <a href="http://www.ruby-doc.org/core-1.9.3/Proc.html" title="ruby-doc.org/core-1.9.3/Proc.html" target="_blank">Proc</a> which is a first class citizen here and can be simply created by adding `Proc.new` in a block.)

Example of a Block in Rails: The code on the left side of the `do` will interact with the variable inside the two pipes on its right side:

```
respond_to do |format|
    format.html #"send index.html"
    format.json {render json: @currencies} #send user currencies info, in .json format
end
```

* * *

#### How does ruby works under the hood?

One thing that uni taught me is that you can’t learn a new programming language if you don’t see how it works under the hood. So, let’s go and have a closer look in there.

The current version of Ruby, Ruby 1.9 (when I write this post in 2013), uses `YARV` (<a href="http://en.wikipedia.org/wiki/YARV" title="wikipedia.org/wiki/YARV" target="_blank">Yet another Ruby VM</a>, also called `KRI`) which is a **bytecode interpreter**. YARV was designed to reduce the execution time of Ruby programs.

When a ruby program is executed, the first step will be to generate YARV instructions. In order to achieve that, the code is first **tokenized**. Then, tokens are grouped into Ruby statements (this step is the **parsing**). And finally, these statements are compiled into low level **YARV instructions**.

Ruby doesn’t use `Yacc` (<a href="http://en.wikipedia.org/wiki/Yacc" title="wikipedia.org/wiki/Yacc" target="_blank">Yet Another Compiler Compiler</a>) as a **parser generator** but <a href="http://en.wikipedia.org/wiki/GNU_bison" title="wikipedia.org/wiki/GNU_bison" target="_blank">Bison</a>. The parser code is processed at build time and not at the run time of the program.

The second step is to run YARV instructions. YARV possesses low level structures. It has for instance two instructions called `branchif` and `branchunless` (which can be understand has low level if and unless Ruby statement) and a low level function called `jump` in order to change the program counter and move into the compiled program.

Now, lets have a look at a simple ruby if/else statement:

```
#login
def create
  user = User.authenticate(params[:email], params[:password])
  if user
    session[:user_id] = user.id
    redirect_to root_url, :notice => "Logged in!"
  else
    flash.now.alert = "Invalid email or password"
    render "new"
  end
end
```

And below are the corresponding YARV instructions:

```
local table (size: 2, argc: 0 [opts: 0, rest: -1, post: 0, block: -1] s1) 
[ 2] user 
0004 putself 
0005 getinlinecache 14, <ic:0> 
0008 getconstant :RubyVM 
0010 getconstant :InstructionSequence 
0012 setinlinecache <ic:0> 
0014 putself 
0015 putobject :create 
0017 send :method, 1, nil, 8, <ic:1> 
0023 send :disasm, 1, nil, 0, <ic:2> 
0029 send :print, 1, nil, 8, <ic:3> 
0035 pop 
0038 getinlinecache 45, <ic:4> 
0041 getconstant :User 
0043 setinlinecache <ic:4> 
0045 putself 
0046 send :params, 0, nil, 24, <ic:5> 
0052 putobject :email 
0054 opt_aref <ic:19> 
0056 putself 
0057 send :params, 0, nil, 24, <ic:7> 
0063 putobject :password 
0065 opt_aref <ic:20> 
0067 send :authenticate, 2, nil, 0, <ic:9> 
0073 setlocal user 
0077 getlocal user 
0079 branchunless 131 
0083 putself 
0084 send :session, 0, nil, 24, <ic:11> 
0090 putobject :user_id 
0092 getlocal user 
0094 send :id, 0, nil, 0, <ic:10> 
0100 send :[]=, 2, nil, 0, <ic:12> 
0106 pop 
0109 putself 
0110 putself 
0111 send :root_url, 0, nil, 24, <ic:13> 
0117 putobject :notice 
0119 putstring "Logged in!" 
0121 newhash 2 
0123 send :redirect_to, 2, nil, 8, <ic:14> 
0129 jump 168 ( 7) 
0135 putself 
0136 send :flash, 0, nil, 24, <ic:15> 
0142 send :now, 0, nil, 0, <ic:16> 
0148 putstring "Invalid email or password"
0150 send :alert=, 1, nil, 0, <ic:17> 
0156 pop 
0159 putself 
0160 putstring "new" 
0162 send :render, 1, nil, 8, <ic:18> 
0170 leave ( 12)
```

One thing or two about this code:

In line `0077`, the `getlocal` instruction will leave a true or false value on the stack. Then, if the condition is false (does the user exist?), the `branchunless` line `0079` will jump to the line `131` where the else is defined to jump the if. If the condition is true, the code just after the `brancheunless` will be compiled and then a jump instruction line `0129` will jump to the end of the method to skip the else.

We can see that at the first line that this snippet starts with `local table`. This is because Ruby encapsulates the execution of code by block. This series of bytecode instructions are specific to this if/else block. A block in Ruby is like a closure. It’s composed by a function to be executed (here the bytecode, internally it’s an **instruction sequence pointer**) and an environment which is a **dynamic frame pointer** (which points right to the stack).
In addition, a block can have an object environment. Internally Ruby represents a block using a **C structure** called `rb_block_t`. YARV creates objects with `RObject` structures which contains a `klass` pointer to the class and an `ivptr` (**instance variable pointer**) which points to an array of instance variable of the object.

* * *

#### Ruby under a microscope

That’s all for now with Ruby, but if you like this kind of things I may suggest you to read the excellent book of Pat Shaughnessy
<a href="http://patshaughnessy.net/ruby-under-a-microscope" title="patshaughnessy.net/ruby-under-a-microscope" target="_blank"><strong>Ruby Under a Microscope</strong></a>
