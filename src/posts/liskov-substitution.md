---
title: The Liskov Substitution Principle
date: '2019-09-27'
---

> "Honor the contract" - Sandi Metz 

I've learned that one of the things about Object Oriented design, especially in Ruby since it's dynamically typed, is that you need to be able to trust the messages being sent by objects. One way to ensure that you are writing trustworthy objects is to follow the Liskov Substitution Principle, the L in SOLID.

This principle states that a subclass must be substitutable for its super class.  Which honestly, puzzled me for days. What's the point of creating a child class if it's going to do the same thing as the parent? I finally understood it when I read "Practical Object Oriented Design in Ruby" and Sandi Metz described how to _test_ LSP. 

LSP helps builds trust because if you know the superclass, then you already know a portion of the child's public interface.  It makes it predictable.  If you create a subclass, and then change that interface, you have broken the contract and now the messages cannot be trusted. You expect it to behave in a certain way because of the super class, but it might not. So then you have to start asking it what kind of object it is and if it has certain things you need so then what's the point of inheriting if you can't trust it?

To test that a subclass has honored the contract, you can create an interface test that the super and all sub classes must pass. You abstract all the superclass public methods into an interface test, and then every child of that class must also pass this test! It doesn't mean that it only has those methods, but it _at least_ has all those methods. If you create a subclass, and extend it, then you can have more specific tests for that subclass, but at the very least it needs to pass the super interface test. 

An example of a super and sub class that violates this principle would be the following:

``` ruby
class Vehicle  
  def initialize  
  end
  
  def turn_on
  end

  def add_gas
  end
end

class Bicycle < Vehicle
  def turn_on
   # nope sorry I don't do that
  end

  def add_gas
   # nope sorry I don't do that either
  end
end
```

If you want to use the bicycle object and you see that it inherits from Vehicle, then you expect those methods to work. In this case, it looks like we might have the wrong abstraction or we're inheriting from the wrong super class. Do we need a non motorized super class that Bicycle can inherit from? Maybe Vehicle should only care about the wheels, and then we can have two subclasses, one for motorized vehicles that would include those two methods and one for non motorized that would not have them. Then Bicycle can inherit from NonMotorizedVehicles and something like Car can inherit from MotorizedVehicles. In the end, they must all pass the Vehicles interface test though.
