# Brandeis University COSI 12B, Spring 2016 PA9

Due date: March 29th, 2016

## Overview
This assignment is to allow you to practice your object oriented design chops.

Turn in an Eclipse export of your working code. The assignment involves designing a series of classes and the interactions between them.

## Background information
When designing applications to work in the real world, the designed needs to think about the real world objects that are involved.

For example, you might need to design an application to manage all the academic information of a school. You need to have information about the courses, the students, the majors, the professors and so on. And you would need to think about what it means for a student to add a course or to drop one. This is often referred to as the *domain model*, those collection of classes and methods that are directly tied to the problem domain. (For example, classes that handle the mouse cursor or how to compress a file are important, but they are not part of the domain model.)

### Domain model
For our purposes, we are going to look at a program to manage a university library. Here is what we know:

  * A library has:
    * a name
    * Some number of floors
   
  * A floor has
    * Some number of cases
   
  * A case has
    * Some number of shelves
   
  * A shelf has
    * zero or more books
    * A capcacity (number of books)
   
  * A book has
    * A title
    * A location


You should use object-oriented programming techniques to model each of these constructs, i.e., map the objects in the library into a proper object-oriented ontology.

## Your task

1. Start with the Eclipse project we provide. It is very incomplete.
1. Design a series of classes to represent the necessary information and behaviors (fields and methods)
1. Extend the abstract `Library` class and implement the required methods according to the documentation
1. Implement the single method in the `LibraryFactory` class, `makeLibrary`. It has four parameters which give you information about the library to construct. The `makeLibrary` method should return a new instance of an object that is a `Library`.


For the `LibraryFactory`, some of the parameters are given as multi-dimensional arrays. Each entry in each multi-dimensional array corrosponds to an object. For example, the last parameter, `shelfCapacity`, gives the capacity of each shelf. `shelfCapacity[i][j][k]` is the capacity of the `k`th shelf of the `j`th case on the `i`th floor. Remember, computer scientists start counting from zero.

The `Library` class also implements the `Iterable<String>` interface. When you extend the `Library` class, you'll additionally need to implement an `iterator()` method. This method should return an iterator over all the books the library knows about, including those that are checked out. However, note that the `getBooksAt` method should return a set of books at the given location, *excluding* those that are checked out. Note that when a book is checked out and checked back in again, it should be returned to the same location.

You will have to use the `BookLocation` class in various methods of your program. It is a very simple [POJO](https://en.wikipedia.org/wiki/Plain_Old_Java_Object) that holds some information about where a book lives.

Once all of that is working, you'll also need to implement the `LibraryFactory::makeLibraryFromFile` method and the `Library.writeToFile` method. The latter should write a JSON representation of your object to the given file, and the former should read said file and reconstruct the `Library` object.


## Example code

Creating a new JSON object:

```java
JSONObject jo = new JSONObject();
List<String> l = new List<String>();

l.add("v1");
l.add("v2");

jo.put("some number", 7");
jo.put("a list", l);

System.out.println(jo.toString());

```

Result:

```json
{ "some number": 7,
  "a list": ["v1", "v2"] }
```

## Testing

Your code can be tested with a provided set of **non-exhaustive** unit tests. That means that if your code fails the tests, it is definitely wrong. The converse is not true: code that can pass the tests is not necessarily correct code.  **Passing the unit tests is not enough to ensure a good score**. You should test your code significantly more on your own. Do not attempt to hard code, corner-case, or otherwise overfit your solution to the tests. We have provided you a subset of the tests that we will use for grading.


##Style guidelines
Follow stylistic guidelines about removing redundancy, using proper data types, indentation, whitespace, identifier names, and commenting at the beginning of your program, on each method, and on complex sections of code.

## Submission
Your exported Eclipse project should be submitted on LATTE. For late policy check the syllabus.

## Grading

You will be graded on:

  * External Correctness: The output of your program should match exactly what is expected. Programs that do not compile will not receive points for external correctness.
  * Internal Correctness:  Your source code should follow the stylistic guidelines shown in class. Also, remember to include the comment header at the beginning of your program.
  * One-on-one interactive grading: By the end of the day that the assignment is due, please make an appointment with your TA for an interactive 10-15 minute grading session. You will receive an email notifying you of the name of the TA who has been assigned to you for this assignment with further instructions on setting up the appointment. (You will be meeting with a different TA for each assignment). One-on-one interactive grading will help you improve your programming skills and avoid repeating mistakes from one assignment to the next.

# Terms and Conditions

<a rel="license" href="http://creativecommons.org/licenses/by-nc-sa/4.0/"><img alt="Creative Commons License" style="border-width:0" src="https://i.creativecommons.org/l/by-nc-sa/4.0/88x31.png" /></a><br /><span xmlns:dct="http://purl.org/dc/terms/" property="dct:title">Cosi12b Assignments</span> by <a xmlns:cc="http://creativecommons.org/ns#" href="http://rmarcus.info" property="cc:attributionName" rel="cc:attributionURL">Ryan Marcus</a> is licensed under a <a rel="license" href="http://creativecommons.org/licenses/by-nc-sa/4.0/">Creative Commons Attribution-NonCommercial-ShareAlike 4.0 International License</a>.
