---
layout: main
title: "Demystifying Singleton Classes and Methods"
header_text: "Demystifying Singleton Classes and Methods"
---

It has been an amazing few weeks at the Flatiron School. Learning how to program has been incredibly fun and exciting as well as frustrating. Aside from the complexity, I think part of the challenge has been the sheer amount of concepts introduced in such a condensed period of time. In an effort to take a deeper dive, I wanted to expand upon an earlier post about Ruby's object-oriented architecture by delving into Singleton classes and methods.

In Ruby, objects are simply instances of their respective classes. Consequently, an object's behavior and attributes are set by whatever is defined in its class. Coupled with inheritance, class definitions provide a powerful framework that give newly created objects immediate functionality. However, what if we wanted a specific instance of an object to have slightly different functionality? In Ruby, Singleton classes and methods allow the programmer to create object instances that are not entirely defined by its class hierarchy. From "Eloquent Ruby" by Russ Olsen, a Singleton method is a method that is unique to a single object.

```ruby
an_array_instance = Array.new

def an_array_instance.count
  puts "Boo!"
end

an_array_instance.count
=> Boo!

an_array_instance.class
=> Array
```

Although similar in appearance, the Singleton method definition differs from traditional method definitions as the receiving object is an object instance (in this case an instance of the Array class). Given that the method is being defined on a specific object, the method is unique to that specific object.

In the example above, the `count` method has been redefined solely for `an_array_instance` to print `"Boo!"`. Also, notice that the object's class has not changed. This is interesting since we know that the `an_array_instance` object does not share the exact same functionality as its class.

How does Ruby reconcile the difference? We know that it cannot be stored in the parent class, otherwise all object instances will possess the same methods. For that exact reason, Ruby supplies a Singleton class that holds any Singleton methods. It can be thought of as a bare class that sits between every object and its class in the inheritance hierarchy - this explains why Singleton methods take precedence over class definitions. The Singleton class has a few unique traits including that it is anonymous and it can not instantiate new objects.

In traditional Ruby fashion, there are a few other ways to create a Singleton method.

Methods can be extended from a module.

```ruby
module Foo
  def foo
    "Yet another method"
  end
end

foobar = []
foobar.extend(Foo)
foobar.singleton_methods
=> [:foo]
```
*Source: [Devalot](http://www.devalot.com/articles/2008/09/ruby-singleton)*

Or, the object can be passed directly to its Singleton class via two less than signs.

```ruby
foobar = []

class << foobar
  def foo
    "Hello World!"
  end
end

foobar.singleton_methods
=> [:foo]
```
*Source: [Devalot](http://www.devalot.com/articles/2008/09/ruby-singleton)*

In terms of application, Rubyists actually use Singleton classes and methods fairly frequently albeit perhaps unknowingly. Whenever a class method is defined, a Singleton method is created because Ruby only supports instance methods. Thus, in order to have class methods, Ruby uses Singleton methods to synthetically create the desired functionality of class methods.

In fact, Ruby classes are themselves just instances of the `class` class. Thus, the class name is just a constant that points to the instantiated object, which stores the corresponding instance method definitions. Class methods are therefore stored in the respective Singleton class.

A less common example of Singleton methods are stubs in Rspec testing. The example below from "Eloquent Ruby" illustrates one use of stubbing.

```ruby
stub_printer = stub :available? => true, :render => nil
stub_font = stub :size => 14, :name => 'Courier'

puts stub_printer.class
=> Spec::Mocks::Mock

puts stub_font.class
=> Spec::Mocks::Mock
```
*Source: Eloquent Ruby*

In closing, Singleton classes and methods provide us with the flexibility to tailor objects when necessary. Despite the seemingly niche application, the Ruby language depends on the Singleton class and method in order to create part of its object-oriented functionality.
