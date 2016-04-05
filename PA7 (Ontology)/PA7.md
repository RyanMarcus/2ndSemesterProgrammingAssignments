# Brandeis University COSI 12B, Spring 2016 PA7

Due date: March 14th, 2016

##Overview
This assignment will teach you about knowledge engineering and AI. Turn in an Eclipse export of your working code. The assignment involves implementing code to compute the shortest route between two points.

##Background information
For a long time, computer scientists have struggled with the apparent gap between what humans can do what we can get computers to do. This fundamental struggle brough about the field of AI. AI and machine learning have lead to some amazing technology recenetly (self-driving cars, expert Go players, natural language systems), but still lack many capabilities that even young children have (emotional empathy, inquisitiveness, self-preservation, etc.). One task that computers are very good at is *transitive inference*. 

If you've ever seen the excellent TV show [Orphan Black](https://en.wikipedia.org/wiki/Orphan_Black), you've seen examples of transitive inference. Questions such as:

> Apples are fruits. Fruits grow are trees. Do apples grow on trees?

Or, slightly more complex:

> All shoes have laces. Some doctors wear shoes. Do all doctors have laces?

The first example represents a series of *unqualified* statements, whereas the second example represents a series of *qualified* statements. An *unqualified* statement is a statement without "all" or "some" in front of it (or, if you'd prefer, an implicit "all").

While it is a fairly simple task for a college-educated human being to answer these questions from the premises described, getting a computer to do it can be a little tricky. In order to get computers to be able to answer unqualified statements, we first define a *knowledge base*, or a set of statements we define as being true.

For example, consider the following knowledge base:

> CS12b is a Brandeis course  
> CS50 is a Harvard course  
> Harvard course is a private college course  
> Brandeis course is a private college course  
> A private college course is a college course  
> A college course is a course  

This knowledge base uses the *is a* linkage in order to establish relationships between various *entities* or *nouns*. From the knowledge base, we can clearly see that CS12b is a course, and that a college course is not CS12b (the *is a* is directional).

This knowledge base can be represented as the following tree:

![graph of KB](http://cs.brandeis.edu/~rcmarcus/cs12b/kb.png)
\

Here, we say that knowledge flows up the tree: everything *is a* of its parent. Once we've turned the knowledge base into a tree, it's easy for a computer to check if some entity `A` *is a* `B`: we simply check to see if `A` is a child of `B`, i.e., if there is a path from `B` to `A`.


With a knowledge tree, we also make something called the *closed world assumption*: we assume that every entity in the world is inside of our knowledge base, and that all entities not listed are nonsense. So, if given the knowledge tree above, if a computer was asked if the easter bunny was a bunny, the computer would (correctly!) respond with `false`.


When discovered, the knowledge tree was a pretty big breakthrough. Computers could figure out that an apple was a fruit, but that an apple wasn't a banana. However, the solution isn't perfect. Consider, instead of using *is a*, if we use the relation *like*:

> Time flies like an arrow  
> Fruit flies like a banana  

Here, the computer might get confused about the two different usages of *like*. This is a pretty big problem for the knowledge tree: we probably can't encode the entire world into a tree, even if we had the time to write down the knowledge base. Luckily, you don't have to create SkyNet for this assignment (that happens in Pengyu Hong's course).

## Your task

Your job is to design and implement a knowledge tree. Specifically, you will need to ensure that these two methods have the appropriate behavior:

  * `void KnowledgeBase::storeIsA(String type, String supertype)`: store information into the knowledge base. Specifically, store the fact that `type` *is a* `supertype`. 
  * `boolean KnowledgeBase::isA(String type, String supertype)`: query the knowledge base to determine if `type` *is a* `supertype`. If either parameter don't exist in the knowledge base, return `false` (closed world assumption).

Remember that *is a* doesn't commute. For example, if I call `storeIsA("apple", "fruit")`, a subsequent call to `isA("apple", "fruit")` should return `true`, but a call to `isA("fruit", "apple")` should return `false`. Also recall that *is a* is transitive, so if I additionally call `storeIsA("fuji", "apple")`, then `isA("fuji", "fruit")` should return `true`. Further, `isA` is reflexive, meaning that an `A` `is a` `A`.

In order to do this, you should construct a knowledge tree as described in the previous section. One way to do this might be to create a class called `Noun` with a method to add a child and another method to check if a particular `Noun` is a child of this `Noun`. The children of `Noun` could potentially be stored inside of a `Set` or a `List` (ask yourself which is more appropriate).


Your code can be tested with a provided set of **non-exhaustive** unit tests. That means that if your code fails the tests, it is definitely wrong. The converse is not true: code that can pass the tests is not necessarily correct code.  **Passing the unit tests is not enough to ensure a good score**. You should test your code significantly more on your own. Do not attempt to hard code, corner-case, or otherwise overfit your solution to the tests. We have provided you a subset of the tests that we will use for grading.



##Style guidelines
Follow stylistic guidelines about removing redundancy, using proper data types, indentation, whitespace, identifier names, and commenting at the beginning of your program, on each method, and on complex sections of code.


##Submission
Your exported Eclipse project should be submitted on LATTE. For late policy check the syllabus.

##Grading

There aren't any unit tests for this assignment. During your 1-1s, your TA will run your code.

You will be graded on:

  * External Correctness: The output of your program should match exactly what is expected. Programs that do not compile will not receive points for external correctness.
  * Internal Correctness:  Your source code should follow the stylistic guidelines shown in class. Also, remember to include the comment header at the beginning of your program.
  * One-on-one interactive grading: By the end of the day that the assignment is due, please make an appointment with your TA for an interactive 10-15 minute grading session. You will receive an email notifying you of the name of the TA who has been assigned to you for this assignment with further instructions on setting up the appointment. (You will be meeting with a different TA for each assignment). One-on-one interactive grading will help you improve your programming skills and avoid repeating mistakes from one assignment to the next.

# Terms and Conditions

<a rel="license" href="http://creativecommons.org/licenses/by-nc-sa/4.0/"><img alt="Creative Commons License" style="border-width:0" src="https://i.creativecommons.org/l/by-nc-sa/4.0/88x31.png" /></a><br /><span xmlns:dct="http://purl.org/dc/terms/" property="dct:title">Cosi12b Assignments</span> by <a xmlns:cc="http://creativecommons.org/ns#" href="http://rmarcus.info" property="cc:attributionName" rel="cc:attributionURL">Ryan Marcus</a> is licensed under a <a rel="license" href="http://creativecommons.org/licenses/by-nc-sa/4.0/">Creative Commons Attribution-NonCommercial-ShareAlike 4.0 International License</a>.
