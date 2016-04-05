# Brandeis University COSI 12B, Spring 2016 PA4

Due date: Feb 22nd, 8am

##Overview
This assignment will give you practice using interfaces and abstract classes. Turn in an Eclipse export of your working code. The assignment involves implementing code to compute simple statistics.

##Background information
In this assignment, you'll create a small statistics package using object-oriented programming techniques. Your final project will be able to compute a number of statistics from data. Data will be stored in subclasses of the abstract `Data` class (one for handling data in an array, and another for handling data from a file). Then, you'll implement several different statistics that fall into three categories:

  * Reductions: operations that map a set of data to a single number (max, min, average)
  * Mappings: operations that map a set of data to another set of data (normalization)
  * Correlators: operations that take two sets of data and compute a single number comparing them (RMSE, Pearson's).

Each of these has widespread applications in science and finance. In general, you'll use packages created by experts to compute such statistics. Thus, this exercise primary purpose is to introduce you to object oriented programming techniques.

### Reductions

Reduction operations are operations that take a set of data and "reduce" them to a single number. They're often called accumulators or folds. One of the most basic reduction operations is a sum, which simply adds up the values in a dataset:

    sum([ 3 1 -2 8 ]) = 10

Another reduction might be a maximum or minimum:

    max([ 3 1 -2 8 ]) = 8

Mathematically, we describe reductions as the successive application of a `reduce` operator, which takes in a `prev`ious value and a `next` value and returns a new value. Initially, the `reduce` operator is called with some `initial` value for `prev`, and the first value of the list for `next`. The reduce operator then returns a new value, which is used as the `prev` value at the next iteration (and the 2nd value of the list is used for the `next` parameter). For a summation, we can define `reduce` as:

    reduce(prev, next) = prev + next, with initial = 0


Using this `reduce` function on the data above would look like this:

 Iteration    `prev`    `next`    return
-----------   ------    ------   --------
     1          0         3         3
     2          3         1         4
     3          4         -2        2
     4          2         8         10

The value of the reduction is the value of the final returned value (in this case, 10).

This simple abstraction is very powerful. For example, we can implement `max` using the following `reduce` function:

    reduce(prev, next) = max(prev, next), with initial = -infinity


For some reductions, however, this abstraction doesn't work very well. For example, to compute the range of a dataset (the difference between the max and the min), you would need to keep track of two values, not just one. When implementing these sorts of reductions, you'll do something a little different.

### Mappings

A mapping is a function that takes a set of data and returns a new set of data of the same size. For example, a linear shift (adding a constant to each data item) or a normalization (making it so all the values are between 0 and 1) are both examples of a mapping.

Mathematically, we define a mapping as a function `map` that takes in a single value `x` and returns a new value. Then, we use the `map` function on each of the elements in the dataset in order to produce a new dataset. For example, a linear shift by `5` could be accomplished with the following `map` function:

	map(x) = 5 + x

All of the mappings you need to implement for this assignment will fit nicely into this abstraction.


### Correlators

A correlator is a function that takes two datasets and returns a single number. [Pearson's correlation for a sample](https://en.wikipedia.org/wiki/Pearson_product-moment_correlation_coefficient#For_a_sample) is one example. Root mean squared error is another.

Mathematically, correlators are defined exactly as you'd expect: a function that takes two datasets and returns a single number.

### Java Reminders

Remember, in Java, there is a difference between a *class* and an *object*. A class is a set of methods and contracts, whereas an object is an *instantiation* of some class. You can think of a class as a blueprint for a house, and an object as the house itself. Thus, we do not write methods that accept "a class of type X", we write methods that accept "objects that are instances of class X".

#### Abstract Classes

An *abstract class* is sort of like an artist's conception of a house instead of a blueprint. It'll have a frame, a roof, windows, and a door, but the specific details of the house -- like the type of concrete used in the foundation, the precise dimensions -- are not specified. An abstract class is a lot like a class, except that it cannot be directly instantiated. Instead of creating instances of abstract classes, we create subclasses of abstract classes and then instantiate those subclasses. For example, consider the following abstract class:

	public abstract class Dwelling {
		private Door d;

		public Dwelling(Door d) {
			this.d = d;
		}
		
		public void openDoor() {
			System.out.println("Opening the door...");
			d.open();
		}
		
		public abstract void openWindow();
	}
		

You should notice a few specific things about abstract classes:

  1. Abstract classes are declared abstract via the `abstract` keyword, as in `public abstract class Dwelling`.
  2. Abstract classes can have constructors, even though they cannot be directly instantiated. It will be the responsibility of the classes extending `Dwelling` to call the constructor in `Dwelling`.
  3. Abstract classes can have *abstract methods*, which are declared via the abstract keyword, as in `public abstract void openWindow()`. Abstract methods are not implemented (there is no method body / curly braces). Any non-abstract class that inherits from `Dwelling` will be required to implement the `openWindow()` method.

For example, consider the following example of a class extending the `Dwelling` class:

	public class OneLevelHouse extends Dwelling {
		
		public `OneLevelHouse(Door d) {
			super(d); // call the superclass constructor
		}
		
		public void openWindow() {
			System.out.println("The windows are always open in the cool house!");
		}

	}


The `OneLevelHouse` class is not abstract, so it can be directly instantiated (via the `new` keyword). Further, instances of the `OneLevelHouse` class will have a `openDoor()` method that can be called. When `openDoor()` is called on an instance of `OneLevelHouse`, the code written in `House` will be called. But, when the `openWindow()` method of the `OneLevelHouse` instance is called, the code written in `OneLevelHouse` will be called.

We've already seen that classes are useful when you have a bunch of objects that will have a lot of code in common, but some code that is different. Abstract classes are useful in the same setting, but with the additional requirement that *the shared code between the classes is meaningless or useless on its own*. In this example, a `Dwelling` is defined as something that has a door that can be opened, but is meaningless without an `openWindow()` method.

#### Interfaces

An interface is like a contract. In general, it contains no or very little actual code. It simply lists methods that must be implementing by any class that `implements` that interface.

For example, consider this interface:

	public interface Speakable {
		public void speak();
		
		public default void silence() {
			System.out.println("I will not be silenced!");
		}
	}
	

The interface `Speakable` defines one method -- `speak()` -- that must be given by any implementing class. The `Speakable` interface also includes one `default` method, `silence()`, which will be "mixed in" to any implementing class. Consider:

	public class Dog implements Speakable {
		@Override
		public void speak() {
			System.out.println("Woof!");
		}
		
		public void fetch() {
			// fetch a ball...
		}

	}

This is a correct implementation of the `Speakable` interface. Given `Speakable` and `Dog`, consider the following:

	public static void main(String[] args) {
		Dog d = new Dog();
		d.speak(); // prints "Woof!"
		d.silence(); // prints "I will not be silenced!"
		d.fetch(); // valid -- will fetch the ball
		
		Speakable s = new Dog(); // you can use the interface as a type
		s.speak(); // prints "Woof!"
		s.silence(); // prints "I will not be silenced"
		s.fetch(); // NOT valid. Speakable does not have a `fetch()`

	}


One key difference between abstract classes and interfaces is that a class can implement more than one interface. If two interfaces both require a method called `x`, then if a class implements both of those interfaces it can provide a single implementation of `x`. However, the same is not true for `default` methods -- the compiler will not allow a class to implement two interfaces with two `default` methods with the same name.

In general, interfaces are used to "tag" classes are capable of a certain action. The `Dog` class might extend the `Animal` class, but might implement the `Domestic`, `Speakable`, and `Adorable` interfaces. These implemented concepts make no sense as any sort of object (either abstract or concrete), and we know that objects will share a mixture of them. When this is the case, interfaces are an appropriate choice.

Interfaces can be very useful when you are trying to write general methods that **apply to objects with a certain property.** Abstract classes can be very useful when you are trying to write general methods that **apply to objects with a similar basic structure.**

In practice, deciding when to use an interface vs. an abstract class is difficult. When it seems ambiguous or arbitrary, interfaces are generally the safer choice **unless you need a constructor,** in which case abstract classes are the only option.


## Your task
This assignment will involve writing a good amount of code. Some of it might seem tedious or redundant. Whenever you feel as though you are writing tedious or redundant code, think about you can reuse another class to solve the problem you are trying to solve. Code reuse is a major advantage of object oriented programming.

You will be required to implement methods in the following classes:

  * `PearsonsCorrelator`: compute the Pearson's correlation for a sample between two datasets
  * `RMSECorrelator`: compute the root mean squared error between two datasets
  * `FileData`: load data from a file
  * `IterableData`: make a `Data` object iterable
  * `MemoryData`: use an in-memory array as a `Data` object
  * `AverageNormMapping`: perform an average norm mapping on a dataset
  * `UnitNormMapping`: scale the values in a dataset to be between 0 and 1
  * `MaxReduction`: calculate the maximum value of a dataset
  * `MeanReduction`: calculate the arithmetic mean of a dataset
  * `MedianReduction`: calculate the median of a dataset
  * `MinReduction`: calculate the minimum of a dataset
  * `RangeReduction`: calculate the range of a dataset
  * `SumReduction`: calculate the sum of a dataset

While you can implement these methods in any order, we highly recommend you "follow along" with this handout and go in the same order as we do. We'll try to take a path that maximizes code reuse so you won't have to write the same code twice.

At any point during the assignment, feel free to run the main method in the `Driver` class. This will use your data objects and your reducers on some toy data in order to show you what is going on. The `main` method also shows off how easy it is to write generic code when you design your project properly using object-oriented methods. Treating classes as their parent types is a very useful feature for writing reusable, generic code.

### Data classes

First, we will implement the data classes. `Data` is an abstract class that represents a named list of `double`s. `FileData` and `MemoryData` will both have to implement `Data`'s two [abstract methods](https://docs.oracle.com/javase/tutorial/java/IandI/abstract.html): `getLength`, which returns the size of the dataset, and `getValueAt(int idx)`, which returns the data item at the given index.

Start with `MemoryData`. In `MemoryData.java`, you'll see an empty constructor and two overriden methods to implement. In the constructor, you should save the passed array of doubles to a private field (which you will need to create), and then implement the `getLength` and `getValueAt` methods. Reminder: to get the length of array `d`, use `d.length`.

After implementing `MemoryData`, you should pass the `testMemoryData()` test in `DataTest`.

Next, move on to `FileData`. Here, you'll write a class that reads in a file specified by a filename and loads data in the following format:

	1.0
	2.0
	8.0
	-12.0

In other words, each line of the file will contain a `double` value. You should read the contents of the file in the `FileData` constructor, and then properly implement `getLength` and `getValueAt`. Reminder: to convert a `String s`(such as those read by a `Scanner` object) to a `double d`, use `double d = Double.valueOf(s);`.

After you implement `FileData`, you should pass the `testFileData` test in `DataTest`.

Next, move on to `IterableData`. This class works as a *wrapper*. It takes in a `Data` object in its constructor, and then makes that `Data` object *iterable* by implementing the `Iterable<Double>` interface (the `<Double>` part says that the type of data being iterated over is `Double`). In Java, iterable objects are objects that can be iterated over using Java's "for each" syntax. For example, consider the following code:

	int[] ary = new int[]{1, 2, 3};
	for (int i = 0; i < ary.length; i++) {
		System.out.println(ary[i]);
	}

Since Java arrays are iterable, the above code can be replaced with:

	int[] ary = new int[]{1, 2, 3};
	for (Integer i : ary) {
		System.out.println(i);
	}

You can [try it out here](https://repl.it/BcUp/0), if you'd like. This syntax is more direct about the programmer's intent, and can reduce indexing and other silly errors.

In Java, you can use a "for each" loop on any object that implements the `Iterable` interface. The only requirement of the `Iterable` interface is that the implementing class have a `iterator()` method that returns an `Iterator` over the proper object. Take a look at [the JavaDocs for `Iterator`](https://docs.oracle.com/javase/8/docs/api/java/util/Iterator.html). Notice that the only two methods that an `Iterator` needs to implement are `hasNext()` and `next()`. The first returns true if there's more data available, and the latter returns the next value and progresses the iterator by one.

The code in this file currently returns a new [anonymous class](https://docs.oracle.com/javase/tutorial/java/javaOO/anonymousclasses.html) that implements `Iterator`. You'll need to write code for the `hasNext()` and `next()` methods. [This StackOverflow post](http://codereview.stackexchange.com/questions/48109/simple-example-of-an-iterable-and-an-iterator-in-java) gives an example of how to make an iterator that does something slightly different. You can also return a valid iterator in any other way you'd like, if you don't want to do it this way.

Once you implement `IterableData`, you should pass the `testIterableData` test in `DataTest`. You should now be passing all the tests in `DataTest`.

### Reduction classes
Next, you should move on to the reduction classes. First, take a look at `Reduction.java`, which contains an abstract class. Examine the code in order to understand how the Java methods in this class map to the `reduce` function explained in the background information section.

The best place to start is the `SumReduction` class. Here, you should implement the `accum` methods and the `initialValue` methods so that the reduction works. After you've implemented these two methods correctly, you should pass the `testSum` test in `ReductionTest`.

Next, move on to `MaxReduction` and `MinReduction`, which will have very similar implementations. Again, properly implement the `initialValue` and the `accum` methods, and then ensure your code passes the appropriate unit tests.

Next, move on to `RangeReduction`. In this class, instead of implementing `initialValue` and `accum`, override the `reduce` method to return the range. You should be able to use your `MaxReduction` and `MinReduction` class to do this in very few lines of code. Again, make sure you pass the `testRange` unit test after you finish.

Next, move on to `MeanReduction` and `MedianReduction`. In both of these classes, you should override the `reduce` method to calculate the mean or the median. Reuse previous reduction operators as needed. Remember, if the number of items in the dataset is odd, the median is the middle value. If the number of items is even, the median is the average of the two center values.

Once you finish, you should be passing all the unit tests in `ReductionTest`.

### Mapper classes
Now we will move on to the mapper classes. Mappers are functions that take a data set to another data set of equal size. Examine the interface in `Mapping.java`. Classes that implement that `Mapping` interface will need to implement a `mapItem` method, and implementing classes will get the `default` `map` method for free. Examine how the `map` method works so you can take advantage of it.

Next, implement `AverageNormMap`. You should compute the average of the dataset in the constructor, and then divide by that average in the `mapItem` method. This means that the mean will not change with successive calls to `map`. This is intended: the `AverageNormMapping` will normalize the given dataset to the average of the dataset it was constructed with.

Finally, implement the `UnitNormMapping` class. This will look very similar to the `AverageNormMap` class. It should linearly scale all the items in the dataset to values between 0 and 1. A technique for doing this is described in the comments. Make sure to reuse your reduction classes wherever applicable. Once you finish, you should pass all the unit tests in `MappingTest`.

### Correlator classes
Almost done! These are pretty straight forward, even though they are the most complicated when you look at the equations. The procedures for implementing both are described in the comments. Again, make sure you pass the unit tests when you are done. Reuse reducers when you can.

For Pearson's correlation, we recommend using the first formula shown on Wikipedia.

![](https://upload.wikimedia.org/math/e/3/c/e3c7ff025788887bba2f3dfca7df94b9.png)
\ 

Recall that a letter with a bar over it represents the mean of that variable. Make sure to take advantage of your existing reducers!



Your reducers, mappers, and correlators can be tested with a provided set of **non-exhaustive** unit tests. That means that if your code fails the tests, it is definitely wrong. The converse is not true: code that can pass the tests is not necessarily correct code.  **Passing the unit tests is not enough to ensure a good score**. You should test your code significantly more on your own. Do not attempt to hard code, corner-case, or otherwise overfit your solution to the tests. We have provided you a subset of the tests that we will use for grading.





##Style guidelines
Follow stylistic guidelines about removing redundancy, using proper data types, indentation, whitespace, identifier names, and commenting at the beginning of your program, on each method, and on complex sections of code.


##Submission
Your exported Eclipse project should be submitted on LATTE. For late policy check the syllabus.

##Grading
You will be graded on:

  * External Correctness: The output of your program should match exactly what is expected. Programs that do not compile will not receive points for external correctness.
  * Internal Correctness:  Your source code should follow the stylistic guidelines shown in class. Also, remember to include the comment header at the beginning of your program.
  * One-on-one interactive grading: By the end of the day that the assignment is due, please make an appointment with your TA for an interactive 10-15 minute grading session. You will receive an email notifying you of the name of the TA who has been assigned to you for this assignment with further instructions on setting up the appointment. (You will be meeting with a different TA for each assignment). One-on-one interactive grading will help you improve your programming skills and avoid repeating mistakes from one assignment to the next. 


# Terms and Conditions

<a rel="license" href="http://creativecommons.org/licenses/by-nc-sa/4.0/"><img alt="Creative Commons License" style="border-width:0" src="https://i.creativecommons.org/l/by-nc-sa/4.0/88x31.png" /></a><br /><span xmlns:dct="http://purl.org/dc/terms/" property="dct:title">Cosi12b Assignments</span> by <a xmlns:cc="http://creativecommons.org/ns#" href="http://rmarcus.info" property="cc:attributionName" rel="cc:attributionURL">Ryan Marcus</a> is licensed under a <a rel="license" href="http://creativecommons.org/licenses/by-nc-sa/4.0/">Creative Commons Attribution-NonCommercial-ShareAlike 4.0 International License</a>.

