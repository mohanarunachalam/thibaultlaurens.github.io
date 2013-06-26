---
layout: post
title: "Generics with C#"
description: "Presentation and how it works under the hood"
category: microsoft
tags: 
- blog-post
---
{% include JB/setup %}

The most important new feature of C# 2.0 (which was released in 2005) is **generics**. Understanding how they are implemented is essential to understand advances and key features they will bring later to
C# and the .NET framework (like **LINQ** for example).

* * *

*What are generics?*  
    Also called parametric polymorphism, generics consists in declaring a type with a type parameter that will be instantiated when it will be needed. It adds a great flexibility to the language.  
    In C#, you can declare generics with the help of **< T >** where T is the parameterized type. Classes, interfaces, methods, properties and delegates can be defined. The exemple below declares a generic simplified version of the List< T > class (available in the .NET framework)
    
<script src="https://gist.github.com/4461585.js?file=list.cs"> </script>

Then, this List< T > can be used by adding objects to the list and using methods of the list.

<script src="https://gist.github.com/4461585.js?file=program.cs"> </script>

An important thing to keep in mind is that parameterized objects has to have **the same type**. If another class is created, an object of this second class cannot be added to the *CarList*.  
Generic with multiple parameterized types can also be declared, in this example a **List< T, U >** could be declared.

* * *

**Constraints** can be added to generics through the **“where”** keyword. It consists in declaring restriction about the parameterized type.
For instance, the parameterized type has to be a reference type or it has to derive from a specific class. These constraints avoid compile-time errors. For more information on constraints on type parameters
check the C# programming guide on the 
<a href="http://msdn.microsoft.com/en-us/library/d5x73970.aspx" title="msdn.microsoft.com/en-us/library/d5x73970.aspx" target="_blank">msdn website</a>.

The website list 6 differents type of constraints:

<table class="responsive">
              <tbody>
              <tr>
                <th><p><strong>Constraint</strong></p></th>
                <th><p><strong>Description</strong></p></th>
              </tr>
              <tr>
                <td><p>where T: struct</p></td>
                <td>
                  <p>The type argument must be a value type. Any value type except Nullable can be specified.</p>
                </td>
              </tr>
              <tr>
                <td><p>where T : class</p></td>
                <td>
                  <p>The type argument must be a reference type; this applies also to any class, interface, delegate, or array type.</p>
                </td>
              </tr>
              <tr>
                <td><p>where T : new()</p></td>
                <td>
                  <p>The type argument must have a public parameterless constructor. When used together with other constraints, the <span><span class="input">new()</span></span> constraint must be specified last.</p>
                </td>
              </tr>
              <tr>
                <td><p>where T : &lt;base class name&gt;</p></td>
                <td>
                  <p>The type argument must be or derive from the specified base class.</p>
                </td>
              </tr>
              <tr>
                <td><p>where T : &lt;interface name&gt;</p></td>
                <td>
                  <p>The type argument must be or implement the specified interface. Multiple interface constraints can be specified. The constraining interface can also be generic.</p>
                </td>
              </tr>
              <tr>
                <td><p>where T : U</p></td>
                <td>
                  <p>The type argument supplied for T must be or derive from the argument supplied for U. </p>
                </td>
              </tr>
            </tbody>
</table>

The example below shows a generic class declaration where the parameterized type has to implement a specific interface.

<script src="https://gist.github.com/4461585.js?file=generic.cs"> </script>

We obtained an error with types that don’t implement “ICustomInterface” like string.

* * *

The C# design team made the choice to integrate generics directly **into the CLR** in order to avoid some problem seen in Java (Java doesn't integrate generics in the JVM and uses type erasure) but also, to guarantee cross language compatibility through
    the .NET framework. C# use **reification** for generics and it results in a complete synchronisation between the compile time and the runtime. In other words, generics instantiation appends at runtime and 
    this is only possible because the IL code produce by the CLR is totally type neutral. Have a look at this excellent 
<a href="http://broadcast.oreilly.com/2009/03/an-interview-with-anders-hejls.html" title="broadcast.oreilly.com/2009/03/an-interview-with-anders-hejls" target="_blank">interview with Anders Hejlsberg</a>
(lead architect of C#, but also author of Turbo Pascal and chief architect of Delphi).

The example below shows a generic list of string and how its type information is preserved in the **IL code**:  

<script src="https://gist.github.com/4461585.js?file=generic.il"> </script>

* It can be noticed here the genericity of the IL code. The “ldarg” instruction for instance (line 5, IL_0000) which takes a method argument and put it on the stack, has no specific type to work with.  
* The line 4 shows the list of string but with a number *List\`1< string >*, this number is the **arity** and correponds to the number of parameterized type used (List< T, U > has an arity of 2).  
* When the parameterized type is used, the IL code add a **!** as it can be seen line 11 (IL_0012)   


* * *

If you have ever played with C# or the .NET framework you should know that **System.Object** is the base class of every .NET classes. Then, you can think that you could use this class instead of creating generic classes, by creating
an **object\[\]** for instance. However, there are reasons why you should always use generics instead of System.Object:
* No boxing/unboxing operations on value types and no cast operations which increase performance, reduce memory consumption and makes the code more readable
* No runtime exeptions, with generics you have a compile-time type check.

The complete technical guide of **Generics** is available online on the msdn website
<a href="http://msdn.microsoft.com/en-us/library/512aeb7t.aspx" title="msdn.microsoft.com/en-us/library/512aeb7t.aspx" target="_blank"><strong>here</strong></a>.



