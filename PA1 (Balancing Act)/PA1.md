# Brandeis University COSI 12B, Spring 2016 PA1

Due date: Jan 25th, 2016

##Overview
This assignment will give you practice with arrays and the basics of Java. Turn in an Eclipse export of your working code. The assignment involves solving "balancing" problems.

##Background information
"Balancing" problems are common in computer science job interviews because they seem easy but actually contain a bit of complexity. All three of the methods you write came from "first-line" job interview questions from real companies. "First-line" questions are questions that companies use to "pre-screen" applicants. If you can solve the "first-line" problems, you're generally invited on-site to demonstrate your ability to tackle more complex technical issues.

##Your task

You will write code for four (4) of the static methods in `WarmupProblems.java`.

  * `int WarmupProblems.countRepeats(int[] items)`: given a sorted list of integers between -1000 and 1000, return the number of duplicate items. For example, for the list `[1, 3, 3, 5, 5, 5, 5]`, countRepeats should return 2, because there are two numbers repeated -- three and five.
  * `boolean WarmupProblems.parensBalance(String s)`: returns true of a string of parentheses is "balanced." For example, the string `()()(())` is balanced, but the string is `)(` is not. If there are any characters besides `(` and `)`, the string is invalid.
  * `boolean WarmupProblems.sum3(int[] items)`: returns true if a set of three items from the array add up to zero. This is a classic problem, and the best possible efficiency [is still an open question.](https://en.wikipedia.org/wiki/3SUM). For example, the set `{5, -7, 2, 4}` has the subset `{5, -7, 2}` which adds up to zero.
  * `boolean WarmupProblems.parensBracketsBalance(String s)`: similar to the `parensBalance` problem, but also including brackets. For example, the string `[()]` is balanced, but the string `[(])` is not. If there are any characters besides `(`, `)`, `[`, `]` the string is invalid.


For each of these methods, and throughout the course, see the JavaDoc comments above the methods for more information. This is mostly a warmup assignment. In general, assignment specifications will be much longer and more detailed.

If you've written your code correctly, you will pass the provided unit tests.

Each of these questions can be tested with a provided set of **non-exhaustive** unit tests. That means that if your code fails the tests, it is definitely wrong. The converse is not true: code that can pass the tests is not necessarily correct code.  **Passing the unit tests is not enough to ensure a good score**. You should test your code significantly more on your own. Do not attempt to hard code, corner-case, or otherwise overfit your solution to the tests. We have provided you a subset of the tests that we will use for grading.




##Style guidelines
Follow stylistic guidelines about removing redundancy, using proper data types, indentation, whitespace, identifier names, and commenting at the beginning of your program, on each method, and on complex sections of code.


##Submission
Your exported Eclipse project should be submitted on LATTE. For late policy check the syllabus.

##Grading
You will be graded on:
  * External Correctness: The output of your program should match exactly what is expected. Programs that do not compile will not receive points for external correctness.
  * Internal Correctness:  Your source code should follow the stylistic guidelines shown in class. Also, remember to include the comment header at the beginning of your program.
  

# Terms and Conditions

<a rel="license" href="http://creativecommons.org/licenses/by-nc-sa/4.0/"><img alt="Creative Commons License" style="border-width:0" src="https://i.creativecommons.org/l/by-nc-sa/4.0/88x31.png" /></a><br /><span xmlns:dct="http://purl.org/dc/terms/" property="dct:title">Cosi12b Assignments</span> by <a xmlns:cc="http://creativecommons.org/ns#" href="http://rmarcus.info" property="cc:attributionName" rel="cc:attributionURL">Ryan Marcus</a> is licensed under a <a rel="license" href="http://creativecommons.org/licenses/by-nc-sa/4.0/">Creative Commons Attribution-NonCommercial-ShareAlike 4.0 International License</a>.
