## Unit Testing

In the last chapter, we discussed the benefits and drawbacks of automated testing compared to to manual testing.  Now, we'll start to dive in to a specific kind of automated testing, unit testing, which will enable you to test the code directly and ensure that individual methods and functions operate in an expected manner.

#### Unit Tests: The Very Idea

What are unit tests?  In a nutshell, they test the smallest "units" of functionality, which in traditional object-oriented programming, usually means things such as methods, functions, or objects.  It's the ultimate in white-box testing; in order to properly write unit tests, you'll need to understand the implementation of a system at a very deep level.  They allow you to ensure that the code that you write does exactly what you intend it do, given a specific input.

Some examples of unit tests might be:

1. Testing that a .sort method returns [1, 2, 3] when you pass in [3, 2, 1]
2. Testing that passing in a null reference as an argument to a function throws an exception
3. Testing that a formatNumber method returns a properly formatted number
4. Checking that passing in a string to a function that returns an integer does not crash the program
5. Testing that an object has .send and .receive methods
6. Testing that constructing an object with default parameters sets the .default attribute to true

As you can see, these functions are testing things which are invisible to the end user.  A user is never going to be in the position of looking at a particular method or object.  They won't care about what methods an object has, or what value variables are set to, or what happens when a null pointer is passed in as an argument.  They will be able to see the _results_ of those things happening, of course, but they won't be able to specifically see the code that caused it.

Unit testing is usually done by the developer writing the code, as part of the software development process.  The developer is intimately familiar with the codebase and should know what data needs to be passed in, which edge cases and corner cases exist, what the boundary values are, etc.  Only occasionally will a white-box tester have to write unit tests in the absence of a developer.

However, it's very important to understand unit testing, even if you are not a developer!  In many organizations, software testers help with unit testing procedures.  This doesn't have to mean writing them; it could be developing tools to help make writing unit tests easier, or verifying that unit tests are testing what the developer thought they were testing, or ensuring that the tests are appropriate and cover all essential cases.

We have seen earlier that the user will never directly see the aspects of the software that unit tests are testing.  What's the point, then?  Wouldn't it make more sense to just test everything from a user perspective?  That way, we'd only catch bugs that impact the user.  We wouldn't have to waste time worrying about things at a low level.  It would probably be more time efficient if we didn't have to write unit tests, right?  Unfortunately, that's not the case.  There are numerous reasons for writing unit tests in addition to systems-level tests (note that this said "in addition to"... there are quite a few problems that unit testing would almost certainly not catch, which is why like all testing strategies, it isn't a silver bullet).

1. _Problems are found earlier_ - Unit tests are normally written along with the code itself.  There's no need to wait for (usually longer-running) systems tests to run, or a manual test to be developer, or even for a build to get into the hands of the testers.  The developer finds problems while still developing.  Like most things, the sooner a problem is found in a process, the cheaper it will be to fix.

2. _Faster turnaround time_ - If a problem is found, there's no need for a build to occur, or to get into testers' hands.  The developer finds a problem when executing the unit test suite, and can immediately go back and start fixing it.  If the developer has to wait for a tester to look at the software, file a defect, and get assigned that defect, it may be quite a while before the developer can actually fix it.  In that time, he or she may have forgotten some implementation details, or at the very least will have probably swapped out some of the information and will require time for a context shift.  The faster a bug can be found, the faster it can be fixed.

3. _Developer understands his/her code_ - Writing tests allows the developer to understand what the expected behavior of the function is.  It also allows the developer to add tests for things that he or she knows might be problematic for a specific function.  For example, a developer may know that a sort function may not sort numeric strings correctly, because certain flags need to be set to ensure that it treats them as numbers instead of strings (so that "1000" is bigger than "99", which it would not be if the function treated them as strings, since "9" is bigger than "1").  A black-box tester may not realize that this is what is happening "under the hood" and so not think to test this particular edge case.

4. _Living documentation_ - The tests provide a kind of "living documentation" to the code.  They explain what a codebase is supposed to do in a different way than the actual code and any comments or documentation for the software.  Failing tests are updated; they are either removed or changed as the softare itself changes.  Unlike traditional documentation, if unit tests are executed on a regular basis (such as before being merged to baseline), then it's impossible for the tests to become obsolete.  Obsolete tests don't pass, and tests that don't pass should not be merged to baseline.

5. _Alternative implementation - One way to think of tests is as a different implementation of the software.  In a sense, a program is just a listing of what a computer should do given certain input - *IF* foo is less than five, *THEN* print out "small foo".  *WHEN* a new bar object is created, *SET* the baz variable to false.  A comprehensive test suite provides a similar service, saying what the program should do in a slightly different way.  There's always room for error, but if you're implementing it twice, once as a test and once as code, then there's less chance that both will be written wrong in exactly the same way.

5. _Able to tell if code changes caused issues elsewhere_ - Programs nowadays can be rather large and complicated, and it's not always easy - or even humanly possible - to know whether a change you're making will have unintended consequences elsewhere.  The author has lost count of how many times he has meant to, say, change the background color of a screen, and caused a stack overflow in some recursive function elsewhere in the code which required the background color to be green for some reason.  However, by having a relatively complete test suite, the developer can check easily if he or she is breaking anything obvious elsewhere in the codebase.  It's not foolproof, but it certainly makes it easier to avoid problems.

#### An Example in Natural Language

Before diving into the code, let's examine a unit test in natural language.  Let's say that we're implementing a Linked List class, and we would like to test equality.  That way, when we make two lists that have the same data in them, they will show as equal, even though they are not the exact same Linked List in memory.  How could we specify a test for this case?

"Create two linked lists, _a_ and _b_, each with data for the nodes equal to 1 -> 2 -> 3.  When there are compared with the equality operator, they should be seen as equal."

We can see that there are three steps here, which correspond to some of the steps in a manual test.  This should not be surprising.  After all, the basic concepts of testing do not change, despite the level of abstraction of a particular test.  The steps are:

1. _Preconditions_ - First, we need to generate the preconditions of the test.  If you recall from several chapters back, the preconditions are those conditions which must be met before the actual test starts.  In this case, the preconditions are that we have two different linked lists, named _a_ and _b_, and that they contain the exact same data, 1 -> 2 -> 3, all of which are the same data type.

2. _Execution Steps_ - This is what is actually done in the test.  In our example, the equality operator is applied between the two linked lists _a_ and _b_, returning a Boolean value.

3. _Expected Behavior_ - Remember that the key principle to testing software is that expected behavior should equal observed behavior.  The unit test should specify what the expected behavior is, then check if it's equal to the observed behavior (i.e., the actual result of the test).  In the world of unit testing, this is called an _assertion_ - the test _asserts_ that the expected behavior is equal to the observed behavior.  In our case, our test will _assert_ that the returned value of comparing the two linked lists is true.

#### Turning Our Example Into a Unit Test

It would be entirely possible to unit test this in a "manual" fashion.  Just generate a quick program which creates a linked list a, creates a linked list b, apply the equality operator,and finally add a conditional checking if the result of that value is true.  If it is, print out "test passed!"; otherwise, print out "test failed!".  However, usually we use a testing framework to automate much of the work and ensure that we are testing in a consistent and coherent manner.

For this book, we will be using the JUnit unit testing framework.  JUnit ( http://junit.org ) is an instance of the xUnit testing framework, which originally comes from SUnit, a testing framework for Smalltalk.  As a side note, Smalltalk is one of those languages that was years ahead of its time.  If you want to see what the future of software engineering will be like, one of the best things to do is to go look at what cool features languages had twenty years ago that were considered too slow or too immature or too "academic".  Usually, these cool features will come back into vogue years later, with the community of whatever language they've ended up in loudly trumpeting their novelty (see: garbage collection, macros, metaprogramming).

Fuddy-duddy rants aside, the JUnit test framework allows us to create unit tests that have much of the "behind-the-scenes" work taken care of.  The developer can then focus on generating the test logic and understanding what is being tested, and instead of wasting time writing out conditionals printing "yay, test passed!" or "boo, test failed" in the appropriate cases.

Although we will be covering JUnit, as it is a popular and easy-to-understand testing framework, it is far from the only unit testing framework in existence.  Just in the world of Java testing, there is TestNG, a more fully-featured framework; JTest, which includes the ability to automatically generate unit tests; and cucumber-jvm, which allows us to behavior-driven testing (and which we'll be discussing in our chapter on that topic).  If you're really interested, just do a search for "testing frameworks _your language of choice_".

Keep in mind, though, that the particular implementation of testing framework you use isn't nearly as important as the concepts you learn and can apply.  Worry less about the syntax, and more about understanding the concepts of unit testing and how they differ, and how they are similar to, concepts that you have already learned in manual testing.

The following is an implementation of a unit test checking for linked list equality, as per above.  Don't worry if you don't understand all of the code; over the next few sections it will be explained thoroughly.

```java
import static org.junit.Assert.*;

public class LinkedListTest

       @Test
       public void testEquals123() {

       	      // Preconditions - a and b both have 1 -> 2 -> 3
       	      LinkedList<Integer> a = new LinkedList<Integer>( [1,2,3] );
	      LinkedList<Integer> b = new LinkedList<Integer>( [1,2,3] );

	      // Execution steps - run equality operator
	      boolean result = a.equals(b);

	      // Expected behavior - assert that result is true
	      assertEquals(result, true);
       }

}
```

#### Preconditions

Here

#### Execution Steps

#### Assertions

Remember that assertions are checking the expected behavior against the observed behavior of the execution steps.  The assert statements on 

#### Examples

#### Ensuring that Tests are Testing What You Expect

One of the simplest ways to do this is to first ensure that your tests fail!  While we'll go into detail on a development strategy that always calls for tests to fail first in the chapter on TDD, a quick change to a test can often prove that it's not just passing all the time because you're mistakenly asserting that `true == true`, for example.

In the linked list equality test above, what could you change to ensure that your tests are testing what you think they are testing?

  What if you changed the first linked list, _a_, to contain the data 1 -> 2?

```java
       @Test
       public void testEquals123() {
       	      LinkedList<Integer> a = new LinkedList<Integer>( [1, 2] );
	      LinkedList<Integer> b = new LinkedList<Integer>( [1, 2, 3] );
	      boolean result = a.equals(b);
	      assertEquals(result, true);
       }
```


Or 7 -> 8 -> 9?

```java
       @Test
       public void testEquals123() {
       	      LinkedList<Integer> a = new LinkedList<Integer>( [7, 8, 9] );
	      LinkedList<Integer> b = new LinkedList<Integer>( [1, 2, 3] );
	      boolean result = a.equals(b);
	      assertEquals(result, true);
       }
```


Or you changed the equality check to an _inequality_ check?

```java
       @Test
       public void testEquals123() {
       	      LinkedList<Integer> a = new LinkedList<Integer>( [1, 2, 3] );
	      LinkedList<Integer> b = new LinkedList<Integer>( [1, 2, 3] );
	      boolean result = !(a.equals(b);)
	      assertEquals(result, true);
       }
```

In all of these instances, the test should fail.  You can then rest a little easier, knowing that your test isn't tautological, passing no matter what the code does.

#### Problems?

Unit testing with the techniques we've learned so far will get us far, but won't get us all the way.  Just using the assertions and testing code that we've gone over so far, there's no way to check, for example, that a particular string will be printed out, or that a window will appear, or that another method is called... all very important things.  After all, if all methods did was return different values with different input, never displaying them to the user or interacting with the environment in any way, we'd have no way of knowing what was happening with our programs.  Our only output would be the generally increasing noisiness of the fan and heat of the CPU.

Behavior aside from returning a specific value is called a "side effect".  Displaying a window, printing some text, connecting to another computer over the network - all of these are, from a terminological perspective, side effects of computation.  Functions and methods without side effects, and that only receive input from parameters, are called "pure"; functions and method with side effects, or who may present different results based on something other than values passed in as parameters, are "impure".  Some languages, such as Haskell, make a strong differentiation between pure and impure functions, but Java does not.

An example of a pure function would be a mathematical function, such as the square root function, as in the following Java code:

```java
public double getSquareRoot(double val) {
       return Math.sqrt(val);
}
```

Assuming no floating-point rounding errors or errors of that nature, the square root of 25 will always be 5, no matter what global variables are set, no matter what time or date it is, no matter what.  There are also no side effects from calling a square root function; it's not as though a window pops up every time your system calculates a square root.

An example of an impure function would be printing out statistics from global variables, or really any method which outputs something to the console or screen, or depends upon variables that are not specifically passed in.  In general, if you see a method with a void return, it's probably impure - a pure function with a void return type would be absolutely useless, since the returned value is its only way of communicating with the rest of the program.  Here is an example of an impure function:

```java
public void printStats() {
       // _connectAttempts is a class-level variable
       System.out.println("Connection attempts: " + _connectAttempts);
}
```

Pure functions are much easier to test, because passing in the same values will always return the same value, and it's easy to test for input and output with standard unit test procedures.

This does not mean that impure functions are bad!  As we've seen, they're absolutely necessary (or, at least, you need some clever way around them, like monads in Haskell) if you 


#### Verification


#### Stubbing

#### Mocking

#### Setup and Teardown

  Oftentimes, there are particular things that you want the tests for a particular class to do before all of the tests start, and after all of the tests end.  For example, you may set up a mocked database connection, and want to use the same one for all of the tests.

  JUnit provides the `setUp()` and `tearDown()` methods for cases such as this.  The `setUp()` method will be called before any @Test-specified unit tests are run, and the `tearDown()` will be called after all the unit tests in that class are run.

#### Testing Private Methods

  There's quite an argument on whether or not it makes sense to test private methods.  It's a great way to start a flame war amongst software developers and testers on your favorite social networking site.  I'll provide you with the arguments of both sides, so that you can make a decision yourself, and then I'll let you know my opinion.

   Those who argue that private methods should never be tested say that any calls from the rest of the program (i.e., the rest of the world from the class's standpoint) will have to come in through the public methods of the class.  Those public methods will themselves access private methods; if they don't, then what's the point of having those private methods?  By testing only the public interfaces of classes, you're minimizing the number of tests that you have and focusing on the tests and code that matter.

   Those who argue tha private methods should always be tested point to the fact that private methods are still code, even if they're not called directly from outside the class.  Unit testing is supposed to test functionality at the lowest levels possible, which is usually the method or function call.  If you're going to start testing higher up the abstraction ladder, then why not just do systems-level testing?

   My opinion is that, like most engineering questions, the correct answer depends on what you're trying to do and what the current codebase is like.  As a side note, with most technical questions, saying "it depends" is a great way to be right, no matter what.  Let's take a look at a few examples.

  Let's imagine that we're adding a cat picture taking service to Rent-A-Cat.

```java

public class Picture {

       private void setupCamera() {
       	       // ...
       }

       private void turnOffCamera() {
       	       // ...
       }

       private Image takePicture() {
               // ...
       }

       public Image takePicture() {
       	      setupCamera();
	      Image i = takePicture();
	      turnOffCamera();
	      return i;
       }

}


```

  This code is relatively simple, and it's easy to see that all of the private methods will be called and tested by the public methods.  From the standpoint of a user of this class, it's easy to test that things work correctly - if a valid image is returned, then the method worked.

  Now let's imagine some code inside the image transformation library might be called from the above code.

```java

public class ImageLibrary {

       public Image transform(Image image, int inSize, int outSize, String format, boolean color, boolean reduce, boolean dither) {
       	      if (inSize < outSize && format.equals("jpg") || dither == false) {
	      	     return privateMethod1(image, inSize, outSize);
	      } else if (inSize < outSize && format.equals("png") || dither == true) {
	      	     return privateMethod2(image, inSize, outSize);
              } else if (outSize > inSize || (color == false && reduce == false) {
	             return privateMethod3(image, inSize, outSize);
	      } else {
	      	     // Imagine lots more if..then..else if statements here
		     // You get the idea
	      }
       }

       // Lots of private methods here

}

```

   Think of all the complicated tests that would be needed for this one method!  Even then, you're not focusing on the actual image transformations.  Many of your tests would just be focused on ensuring that the right method was called!

  One could argue that this isn't a well-designed piece of code and should be re-factored, ideally with the private methods put into, say, an ImageTransformer class, where the methods would be public and could easily be unit tested.  I wouldn't disagree.  However, the fact of the matter is that in the real world, there is often code like this lying around, and the tester is not always in a position to tell management that the company needs to spend a few months burning off technical debt instead of adding new features.  If your goal is to test the software, and test it well, you'll probably have to test the occasional private method.

#### Using Reflection to Test Private Methods in Java

  In Java, there's no way to directly call private methods from a unit test, although this is definitely not the case in other languages (such as with Ruby's .send(:method_name) method, which bypasses the concept of "private" entirely).  However, using the reflection libary, we can "reflect" what's the structure of the class is at runtime.  The reflection library is built in to the Java language, so you don't need to install anything else to use it.

  Let's give an example - if you've never worked with reflection before, it can be a bit strange.  Say we want to write a class which tells the user what methods are available in that class.  Without reflection, this is impossible in Java; how can you know what other methods exist without hard-coding them into a String or something along those lines?  However, this is very simple using reflection.

```java

import java.lang.reflect.Method;

public class ReflectionFun {

	public void printQuack() {
		System.out.println("Quack!");
	}

	public static void main(String[] args) {

		Method[] methods = ReflectionFun.class.getMethods();

		// Get all methods from class and any from superclasses callable
		// on this class.
		System.out.println("All methods:");
		for (Method method : methods){
		    System.out.println(method.getName());
		}
	}
}
```

  When we run this program, we get the following output:

```
All methods:
main
printQuack
wait
wait
wait
equals
toString
hashCode
getClass
notify
notifyAll
```

  This is pretty cool, we can then do things like check to see if a method exists before calling it, or let the programmer know what methods exist.  If you've ever used a language like Ruby, where you can quickly check what methods are available on an object, you can see how useful this can be.  If you're new to a codebase, and you know that you want to do something related to quacking, but you're not sure if you want to call displayQuack(), or quackify(), or quackAlot(), or whatever, you can do a quick method listing and see that the method you are looking for is printQuack.

  You may have noticed that there are many more methods here than are listed in the ReflectionFun class.  This is because the getMethods() method returns a list of _all_ methods callable on an object (that is, public methods).  Since all objects in Java descend from the Object class, any of the public methods on the Object class will also appear here.

   You'll also note that there are three different "wait" methods listed.  This is simply because Java considers methods with the same name but different argument lists as different methods.  Reviewing the Java API, we can see that the following three methods exist:

```java
public void wait();
public void wait(long timeout);
public void wait(long timeout, int nanos);
```

  Reviewing the code above, you can see that the methods[] array actually contains methods as objects!  This may not seem strange in a functional language, but if you are only used to straightforward Java programming, it may seem a bit weird.  If the concept of a method or function existing as a first-class object seems strange to you, my first recommendation is to learn Haskell or another functional programming language until it seems like second nature.  If you don't particularly have the time for that, just think of them as functions that you can pick up and carry around, then do something with them at a later date, instead of having them only be in one place.

  Now that we have this list of methods, we can actually invoke them by name, by passing in the name of the method to which we'd like to have a reference to the getMethod() method.

```java

import java.lang.reflect.Method;

public class ReflectionFun {

	public void printQuack() {
		System.out.println("Quack!");
	}

	public static void main(String[] args) {
	       try {
			System.out.println("Call public method (printQuack):");
			Method method = ReflectionFun.class.getMethod("printQuack");
			Object returnValue = method.invoke(rf);

		} catch (NoSuchMethodException nsmex) {
			System.err.println("No such method!");
		}
	}
}
```

  This displays:

```
Call public method (printQuack):
Quack!
```

   Using this, you could add a way to manually test and call methods, by having the user enter a string and trying to call a method by that name on the object.  The point is, we now have run-time control of what methods to call.  This is very useful for metaprogramming and programmer interfaces such as REPLs (read-eval-print-loop systems, which lets you enter some code, see the results, and repeat).  However, so far, it has not helped us much with testing private methods.  However, now that you understand reflection, some minor tweaks to our existing code and we can access them easily.

  First, you can't use the `getMethod()` or `getMethods()` methods, as they only return publicly available methods.  Instead, you need to use `getDeclaredMethod()` or `getDeclaredMethods()` methods, which have two key differences.

1. They only return methods declared in that specific class.  They will not return methods defined in superclasses.
2. They return public, private, and protected methods.

  Therefore, if we wanted a list of _all_ methods defined on ReflectionFun, we could use the getDeclaredMethods() method.  Just for fun, let's also add a private `printQuock()` method to go along with our public `printQuack()` method (my definition of "fun" may be slightly different than yours).


```java

import java.lang.reflect.Method;

public class ReflectionFun {

	public void printQuack() {
		System.out.println("Quack!");
	}

	public void printQuock() {
	       System.out.println("Quock!");
	}

	public static void main(String[] args) {

	       System.out.println("Declared methods:");
	       Method[] methods = ReflectionFun.class.getDeclaredMethods();
	       for(Method method : methods){
	       	       System.out.println(method.getName());
	       }

	}
}
```

  The output of this program is:

```java
Declared methods:
main
printQuack
printQuock
```

  Since we once again have just a list of Method objects, we can invoke them.  There's only one small snag - we first need to set that method to "accessible" before calling it, using the setAccessible() method.  It accepts a boolean parameter to determine whether or not the method should be accessible outside the class.

```java

import java.lang.reflect.Method;

public class ReflectionFun {

	public void printQuack() {
		System.out.println("Quack!");
	}

	private void printQuock() {
		System.out.println("Quock!");
	}

	public static void main(String[] args) {
	       try {
			System.out.println("Call private method (printQuock):");
			Method method2 = ReflectionFun.class.getDeclaredMethod("printQuock");
			method2.setAccessible(true);
			Object returnValue = method2.invoke(rf);
		} catch (NoSuchMethodException nsmex) {
			System.err.println("No such method!");
		}
	}
}
```

  This will output:

```
Call private method (printQuack):
Quock!
```

  We can combine this with the other unit testing we've learned earlier in the chapter to write a unit test for a private method.

```java
// TEST HERE
```

#### Unit test structure

#### Code Coverage

#### Best Practices when Unit Testing

#### Unit Testing as Part of a Complete Testing Plan

Just like your favorite nutritionally-suspect cereals, unit testing should not be your entire breakfast, or your entire test plan.  Unit testing is great for checking individual methods and low-level functionality, but it is not good at seeing how everything fits together.  It's even worse when trying to determine what the end product will look like; all of the individual methods may work, but together they form something which doesn't meet any of the requirements.