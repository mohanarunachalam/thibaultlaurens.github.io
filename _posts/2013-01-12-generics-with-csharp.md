---
layout: post
title: "Generics in C#"
description: "How it works under the hood"
category: microsoft
---
{% include JB/setup %}

One of the most important new feature of C# 2.0 (released in 2005) was the introduction of **generics**. Knowing how they are implemented is essential to understand advances and key features they will bring later to C# and the .NET framework (technologies like `LINQ` for example).

* * *

#### What are generics? ####

Also called parametric polymorphism, generics consists in declaring a type with a type parameter that will be instantiated when it will be needed. It adds a great flexibility to the language. 
 
In C#, you can declare generics with the help of `<T>` where `T` is the parameterized type. Classes, interfaces, methods, properties and even delegates can be used as a generic type. The exemple below declares a simplified version of the `List<T>` class (available in the .NET framework).


```
public class List<T>
{
    private T[] items;
    private int size;

    public List()
    {
        //the capacity of the list is not implemented in this simple version
        this.items = new T[10];
    }

    public void Add(T item)
    {
        this.items[this.size++] = item;
    }

    public void Remove(T item)
    {
        //get the index of the item
        int index = Array.IndexOf<T>(this.items, item, 0, this.size);
        --this.size;
        if (index < this.size)
            //removes the item by puting elements after it at its index
            Array.Copy((Array)this.items, index + 1, (Array)this.items, index, this.size - index);
        this.items[this.size] = default(T);
    }

    // Gets or sets the element at the index
    public T this[int index]
    {
        get
        {
            if (index >= this.size)
                throw new Exception("Argument out of range");
            return this.items[index];
        }
        set
        {
            if (index >= this.size)
                throw new Exception("Argument out of range");
            this.items[index] = value;
        }
    }

    public int Count
    {
        get { return this.size; }
    }
}
```

We can then instantiate this `List<T>` and start using it by adding objects to it and using all the methods available.

```
public class Car
{
    public string Name { get; set; }
}

class Program
{
    private static void Main(string[] args)
    {
        List<Car> CarList = new List<Car>();

        Car bmw = new Car { Name = "BMW" };
        Car mercedes = new Car { Name = "Mercedes" };

        CarList.Add(bmw);
        CarList.Add(mercedes);

        CarList.Remove(mercedes);

        Console.WriteLine("Number of cars in the list: " + CarList.Count);
        Console.ReadLine();

    }
}
```

An important thing to keep in mind is that parameterized objects have to have **the same type**. In this example, if another class is created, an object of this new class cannot be added to the `CarList`.
  
However, generic with multiple parameterized types can also be declared, in this example a `List<T, U>` could be declared.

* * *

**Constraints** (basicaly restriction about the parameterized type) can be added to generics with the help of the `where` keyword.

Constraints could mean that the parameterized type has to be a reference type or that it has to derive from a specific class for example. These constraints avoid compile-time errors. For more information about them, check out the C# programming guide on the 
<a href="http://msdn.microsoft.com/en-us/library/d5x73970.aspx" title="msdn.microsoft.com/en-us/library/d5x73970.aspx" target="_blank">msdn</a> website.

It lists 6 differents type of constraints:

| Constraint                   | Description  |
| ---------------------------- | --------------------------------------------------------------------------- | 
| where T : struct             | The type argument must be a value type. Any value type except Nullable can be specified. | 
| where T : class              | The type argument must be a reference type; this applies also to any class, interface, delegate, or array type. | 
| where T : new()              | The type argument must have a public parameterless constructor. When used together with other constraints, the new() constraint must be specified last. | 
| where T : \<base class name> | The type argument must be or derive from the specified base class. | 
| where T : \<interface name>  | The type argument must be or implement the specified interface. Multiple interface constraints can be specified. The constraining interface can also be generic. | 
| where T : U                  | The type argument supplied for T must be or derive from the argument supplied for U. | 
{: .table .table-bordered .table-hover .table-condensed}

The example below shows a generic class declaration where the parameterized type has to implement a specific interface.

```
interface ICustomInterface
{ }
public class Class2 : ICustomInterface

public class CustomList<T> where T : ICustomInterface
{
    void AddToList(T element)
    {}
}

public class Class1
{
    public Class1()
    {
        CustomList<string> myCustomList1 = new CustomList<string>();
        CustomList<Class2> myCustomList1 = new CustomList<Class2>();
    }
}
```

We will get an error with types that don’t implement “ICustomInterface”, like the type `string` here.

* * *

The C# design team made the choice to integrate generics directly into the **CLR** in order to avoid some problems seen in Java (Java doesn't integrate generics directly in the JVM and uses type erasure) but also to guarantee cross language compatibility throughout the .NET framework.
 
C# uses **reification** for generics and it results in a complete synchronisation between the compile time and the runtime. In other words, generics instantiation appends at runtime and this is only possible because the **IL** code produced by the CLR is totally type neutral. Have a look at this excellent 
<a href="http://broadcast.oreilly.com/2009/03/an-interview-with-anders-hejls.html" title="broadcast.oreilly.com/2009/03/an-interview-with-anders-hejls" target="_blank">interview</a> with Anders Hejlsberg (lead architect of C#, but also author of Turbo Pascal and chief architect of Delphi).

The example below shows a generic list of string and how its type information is preserved in the IL code: 

```
{
  // Code size    24 (0x18) 
  .maxstack 2
  .locals init ([0] class [mscorlib]System.Collections.Generics.List`1<string> cars) 
  IL_0000:  ldarg.0
  IL_0001:  call    instance void [mscorlib]System.Object::.ctor()  
  IL_0006:  newobj  instance void [mscorlib]System.Collections.Generic.List`1<string>::.ctor()
  IL_000b:  stloc.0
  IL_000c:  ldloc.0
  IL_000d:  ldstr   "bmw"
  IL_0012:  callvit instance void class [mscorlib]System.Collections.Generic.List`1<string>::Add(!0)
  IL_0017:  ret
} // end of method Class1::.ctor 
``` 

- Here we can see the genericity of the IL code: The `ldarg` instruction for instance on line 5 (IL_0000) which takes a method argument and put it on the stack, has no specific type to work with.  
- The line 4 shows the list of string but with a number `List'1<string>`, this number is the **arity** and correponds to the number of parameterized type used (`List<T, U>` has an arity of 2).  
- When the parameterized type is used, the IL code add a `!` as it can be seen line 11 (IL_0012)   

* * *

If you have ever worked with C# or the .NET framework you should know that `System.Object` is the base class of every .NET classes. One could think that we could use this class instead of creating generic classes, by creating an `object[]` for instance. 

However, there are reasons why you should always use generics instead of System.Object: 

- No boxing/unboxing operations on value types and no cast operations, which increases performance, reduce memory consumption and makes the code more readable.  
- No runtime exeptions: with generics you have a compile-time type check.

For more info, the complete technical guide of generics is available online on the msdn website
<a href="http://msdn.microsoft.com/en-us/library/512aeb7t.aspx" title="msdn.microsoft.com/en-us/library/512aeb7t.aspx" target="_blank"><strong>here</strong></a>.



