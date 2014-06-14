---
layout: main
title: "A Small Case Study of Objects & Classes in Ruby"
header_text: "A Small Case Study of Objects & Classes in Ruby"
---

While flipping through a beginner ruby presentation on SpeakerDeck, I came across a slide with the following expressions in Ian Bishop's "Let's Talk About Ruby" presentation.

    /abc/ === "abc"
    => true

    "abc" === /abc/
    => false

What?!?! How can that be?

After some research, I was finally able to make sense of the expressions and their respective return values. Before diving into the interpretation of the expressions, it's important to take a slight detour into the inner workings of Ruby. We know that "everything in Ruby is an object", but what does that really mean?

Objects have two distinct characteristics: state and behavior. State defines the attributes of the object (i.e. data about the object), while behavior enables the object to interact with other objects. In Ruby, variables are used to store the attributes of an object, while methods enable the objects to interact with other objects.

How are objects created? "Classes" in Ruby serve as the blueprint for creating new objects. Rather than having to define state and behavior for every object created, Ruby allows the programmer to preset attributes and behavior. Consequently, one set of definitions can be used to create new instances of similar objects. This construct is analogous to animals in the real world. For example, all dogs share a base level of attributes and behaviors. By creating a Dog class, the programmer can group all of those definitions under one roof thereby consolidating the code and following the DRY (Don't Repeat Yourself) principle.

Immediately upon creating a new instance of an object, the object only possesses whatever state and behavior bestowed on it by its parent class. If multiple ancestors exist, the object will inherit the higher level methods as well.

Now that we have a basic understanding of objects in Ruby, we can begin to break down the initial slide. The most common syntax for invoking a method is to chain the method name to the object with a period. In the below example, the entire string preceding the period is an object, and is also considered to be the receiver. 

    "I'm a String Object!".length
    => 20

    # Receiver_Object.Method_Name

Alternatively, a method can be called by sending a message to the object.

    "I'm a String Object!".send(:length)
    => 20

In true Ruby fashion, there is yet another way of executing the above commands. Since everything is an object, you can also instantiate a new method object and then call it.

    string = "I'm a String Object!".method(:length)
    string.call
    => 20

Interestingly, you may be wondering where the method is in the original slide. The `==` and `===` operators are in fact disguised as methods. That means the following syntax also works.

    "I'm a String Object!".== "I'm a String Object!"
    => true

    "I'm a String Object!".send(:==, "I'm a String Object!")
    => true

    string = "I'm a String Object!".method(:==)
    => #<Method: String#==> 
    string.call("I'm a String Object!")
    => true

Given the common nature of the usual math operators, Ruby conveniently allows the methods to be called without the period notation.

Returning to the original expression, we now know that the receiver is the object on the left hand side of the method `===`. I then ran the following code.

    /abc/.class
    => Regex

    /abc/.ancestors
    => [Regexp, Object, Kernel, BasicObject]

    "abc".class
    => String

    "abc".ancestors
    => [String, Comparable, Object, Kernel, BasicObject]

So, for the first expression, we know that the receiving object is of the class Regex. Alternatively, the second expression is of the class String. We also know that both classes are sub-classes of Object. Looking into the documentation for the Regex, String, and Object classes, we find that all classes define instance methods for the `===` operator.

The Object instance method defines triple equals as, "For class Object, effectively the same as calling `==`, but typically overridden by descendants to provide meaningful semantics in case statements." Next, the Regex instance method defines triple equals as "Case Equality — Used in case statements." Finally, the String instance method says that the `==` and `===` operators test for equality and share the same implementation.

As we briefly touched on before, objects inherit from ancestor classes. This can create conflicts when multiple classes define the same method name. In order to resolve the conflict, Ruby takes the most recent definition of the method. Consequently, in our example, we now see that we are calling the `===` operator on two different objects, which both happen to define the `===` method differently.

The first expression is of the Regex class, so we know that the `===` works similarly to case statements. From the newcomer's guide in the Ruby doc, "the case statement actually invokes the `===` method, not the `==` method. Looking at the example below, the order of the method call is `object_to_compare_1 === object` and not `object === object_to_compare_1`." This order is important because the receiving object determines the method behavior, which actually makes the case statement a little more flexible.

    case object
    when object_to_compare_1
    # code
    when object_to_compare_2
    # code
    end

Back to our example, since the method call is on a Regex object, the method is testing whether the `"abc"` matches the pattern in `/abc/`. Since we know that is true, the expression returns true.

Moving on to the second expression, we now know that the `===` operator is analogous to the `==` operator for Strings. Since `/abc/` is not equal to `"abc"`, the second expression returns false.
