---
layout: post
title: "A Small Case Study of Objects & Classes in Ruby"
date: 2013-10-07 22:55
comments: true
published: false
categories: 
---

While flipping through a beginner ruby presentation on SpeakerDeck, I came across a slide with the following expressions in Ian Bishop's "Let's Talk About Ruby" presentation.

```ruby
/abc/ === "abc"
=> true

"abc" === /abc/
=> false
```

What?!?! How can that be?

After some research, I was finally able to make sense of the expressions and their respective return values. Before diving into the interpretation of the expressions, it's important to take a slight detour into the inner workings of Ruby. The title of the slide, "Everything is a message", served as the first hint, and pointed me towards understanding what being an object-oriented language like Ruby really means.

Here we go. Nearly everything in Ruby is considered to be an object. Objects have two distinct characteristics: state and behavior.  State defines the attributes of the object (i.e. data about the object), while behavior enables the object to interact with other objects.  Variables are used to store the attributes of an object, while methods enable the objects to interact with other objects.

How are objects created? Ruby has what are called "classes" that serve as the blueprint for creating new objects. Rather than having to define state and behavior for every object created, Ruby allows the programmer to create classifications for objects. That way one set of definitions can be used for all instances of the same class. Classes in Ruby are analogous to classes in the real world. For example, all dogs share certain attributes and behaviors. By creating a Dog class, the programmer can group all of the related attributes and methods under one roof. This construct allows Rubyists to maintain the principle of DRY (Don't Repeat Yourself) where new instances of a dog can be created with a simple call to the Dog class.

Immediately upon creating a new instance of an object, the object only possesses whatever state and behavior bestowed on it by its parent class. If multiple ancestors exist, the object will inherit the higher level methods as well.

Now that we have a basic understanding of objects in Ruby, we can begin to break down the initial slide. The most common syntax for invoking a method is to chain the method name to the object with a period. In the below example, the entire string preceding the period is an object, and is also considered to be the receiver. 

`"I'm a String Object!".length`

Alternatively, a method can be called by sending a message to the object.

`"I'm a String Object!".send(:length)`

Or, you can also instantiate a new method object and then call it.

`string = "I'm a String Object!".method(:length)`
`string.call`

Interestingly, you may be wondering where the method is in the slide. The `==` and `===` operators are in fact disguised as methods. That means the following should work.

```ruby
"I'm a String Object!".== "I'm a String Object!"
"I'm a String Object!".send(:==, "I'm a String Object!")
string = "I'm a String Object!".method(:==)
string.call("I'm a String Object!")
```

After running the code in IRB, you will notice that they all return the correct values! Given the common nature of the usual math operators, Ruby conveniently allows the methods to be called without the period notation.

Returning to the original expression, we now know that the receiver is the object on the left hand side of the method `===`. I then ran the following code.

```ruby
/abc/.class
=> Regex

/abc/.ancestors
=> [Regexp, Object, Kernel, BasicObject]
```

So, we know that object is of the class Regex and has several ancestors. Looking into the documentation for the Regex and Object classes, we find that both classes define instance methods for the triple equals operator. 

The Object instance method: "Case Equality – For class Object, effectively the same as calling #==, but typically overridden by descendants to provide meaningful semantics in case statements."

The Regex instance method: "Case Equality — Used in case statements."

However, since Regex is the most immediate class, the Object inherits the method definition from Regex. The last bit of reference comes from how the case statement executes (described below).

```ruby
case obj
when obj_1
  # code
when obj_k
  # code
end
```

The case statement actually invokes the `===` method, not the `==` method. Further, the order of the method call is `obj_k === obj` and not `obj === obj_k`.

The reason for this order is so that the case statement can "match" obj in more flexible ways. Three interesting cases are when obj_k is either a Module/Class, a Regexp, or a Range: The Module/Class class defines the "===" method as a test whether obj is an instance of the module/class or its descendants ("obj#kind_of? obj_k"). The Regexp class defines the "===" method as a test whether obj matches the pattern ("obj =~ obj_k"). The Range class defines the "===" method as a test whether obj is an element of the range ("obj_k.include? obj").

Since `"abc"` matches the pattern `/abc/`, the first expression returns true.

Looking into the second expression, we see that the `===` operator is being called on a String. 

```ruby
"abc".class
=> String

"abc".ancestors
=> [String, Comparable, Object, Kernel, BasicObject]
```

The String instance method defines the `===` operator with the following definition: "Equality—If obj is not a String, returns false. These two methods, == and ===, share the same implementation."

Since `/abc/` is not equal to `"abc"`, the second expression returns false.