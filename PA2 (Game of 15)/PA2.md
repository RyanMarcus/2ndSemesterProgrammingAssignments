# Brandeis University COSI 12B, Spring 2016 PA2 (update 1)

Due date: Monday, Feb 1st at 10am

##Overview
This assignment will give you practice using an object that has been provided to you. Turn in an Eclipse export of your working code. The assignment involves writing an interface and an AI to play a simple game.

##Background information

The Game of 15, or [15-puzzle](https://en.wikipedia.org/wiki/15_puzzle), involves 15 tiles that can slide around in a 4x4 box.

![](http://cs.brandeis.edu/~rcmarcus/cs12b/board1.png)
\ 

The image above shows a 15-puzzle in a solved position. The numbers are sorted in ascending order from left to right and then from top to bottom. A puzzle is only considered "solved" when it is in this exact state. Consider the following board, representing an unsolved 15-puzzle:




 C1    C2    C3    C4
----  ----  ----  ----
 1     2     3     4
 5     6     7     8
 9     10    11    12
 13    14          15


Solving this puzzle is fairly simple, as only one tile is misplaced. In order to solve this puzzle, we simply slide the "15" tile to the left. We call this moving left.


 C1    C2    C3    C4
----  ----  ----  ----
 1     2     3     4
 5     6     7     8
 9     10    11       
 13    14    15    12


This puzzle is also only a single move away from being solved. In order to solve this puzzle, slide the "12" tile upwards. We call this moving up.


 C1    C2    C3    C4
----  ----  ----  ----
 1     2     3     4
 5     6           7
 9     10    11    8   
 13    14    15    12

In most cases, solving a puzzle will require multiple moves. In order to solve the puzzle above, we have to move the "7" tile to left, then move the "8" tile upwards, then move the "12" tile upwards.

In the Game of Fifteen, there are four possible moves:

  * `up`: slide a tile upwards into the blank space. This move cannot be performed if the blank spot is in the bottom row.
  * `right`: slide a tile to the right into the blank space. This move cannot be performed if the blank spot is in the leftmost column.
  * `down`: slide a tile downward into the blank space. This move cannot be performed if the blank spot is in the topmost row.
  * `left`: slide a tile to the left into the blank space. This move cannot be performed if the blank spot is in the rightmost column.

One interesting property of the 15-Puzzle is that not all puzzle states can be solved. For example, consider the puzzle below:

![](http://cs.brandeis.edu/~rcmarcus/cs12b/board2.png)
\ 

No matter how many valid moves you make, you'll never be able to get the puzzle into a solved state. For a proof, [see the Wikipedia page](https://en.wikipedia.org/wiki/15_puzzle).

##Your task

In this assignment, you'll be required to implement two "front ends" for the 15-puzzle. The first will allow a human player to play the game, and the second will be a computer AI that plays the game.

We have provided you with a class called `GameOf15`, located in `GameOf15.java`, which contains all the code needed to store boards, generate solvable games, conduct slides, and test to see if the puzzle is solved. You may look at the code, but you *must not modify it*. The goal of this assignment is to give you experience working with objects provided by others.

The `GameOf15` class has several methods you will need:

  * `GameOf15::hasWon()` returns true if the puzzle has been solved, and false otherwise.
  * `GameOf15::moveUp()` will slide the piece below the blank space upwards. If this cannot be done, nothing happens.
  * `GameOf15:moveLeft()` will slide the piece to the right of the blank space to the left. If this cannot be done, nothing happens.
  * `GameOf15::moveDown()` will slide the piece above the blank space downwards. If this cannot be done, nothing happens.
  * `GameOf15::moveRight()` will slide the piece to the left of the blank space to the right. If this cannot be done, nothing happens.
  * `GameOf15::getValue(int row, int col)` will return the tile at the given row/column index of the board. The blank space is represented by zero.
  * `GameOf15::toString()` will return a string representation of the board that you can display to the user

###Part 1: The Human Player
You should start by implementing the `HumanPlayer::play` method, located in `HumanPlayer.java`. In this method, you should create a new `Scanner` and continuously execute the following procedure:

  1. Print out the current game board, which can be done by executing `System.out.println(gof);`. This takes advantage of the provided `toString()` method in the `GameOf15` class.
  2. Prompt the user to enter one of: `l` for left, `u` for up, `r` for right, or `d` for down, followed by the return key. You may assume the user always provides valid input.
  3. Execute the move indicated by the user.
  4. If the game has been solved, print out the game board,  print "You win!", and then exit the loop. Otherwise, continue.

Here's an example session:

```
1	2	3		
5	6	7	4	
9	10	11	8	
13	14	15	12	
 
Enter a move: (l)eft, (u)p, (r)ight, or (d)own: u
1	2	3	4	
5	6	7		
9	10	11	8	
13	14	15	12	
 
Enter a move: (l)eft, (u)p, (r)ight, or (d)own: u
1	2	3	4	
5	6	7	8	
9	10	11		
13	14	15	12	
 
Enter a move: (l)eft, (u)p, (r)ight, or (d)own: u 
1	2	3	4	
5	6	7	8	
9	10	11	12	
13	14	15		
 
You win!

```

You should be able to run your code and play! You should also pass all of the tests in `HumanPlayerTest.java`.

**Extra credit**: write the `HumanPlayer::play` method without: additional methods, additional classes, if statements, `switch` statements, or more than a single loop. It can be done. Our solution is 14 lines long. You may use standard Java libraries. If you figure it out, email Ryan at `rcmarcus@brandeis.edu` and he will check your submission and award you extra credit. If you don't email him, you will miss out on your extra credit!

###Part 2: The Computer AI

For the second part of the assignment, you'll implement a simple AI to solve a relaxed version of the 15-puzzle. In the `ComputerPlayer::solve` method, write code that will move the blank space from wherever it begins to the bottom right, and print out the moves needed as a sequence of newline-seperated `u`, `l`, `r`, and `d`s.

If the `solve` method is given the following board:


 C1    C2    C3    C4
----  ----  ----  ----
 1     2     3     4
 5     6     7     8
 10    13    11    12   
 9           14    15

Then the output of your code should be:

```
l
l
```

Performing two "left" moves will cause the blank space to be in the bottom right, which, for this assignment, will be considered acceptable. In addition to printing out the needed moves, you should also perform the moves on the board using the appropriate methods and return the board.

You can run this provided `main` method to test your code on a random board. After you complete this task, you should pass the tests in `ComputerPlayerTest.java`.

**Extra credit**: make your `solve` method solve the entire puzzle. Hint: no solvable board requires more than 81 moves to solve. There are a few more hints in the comments above the method as well. If you figure it out, email Ryan at `rcmarcus@brandeis.edu` and he will check your submission and award you extra credit. If you don't email him, you will miss out on your extra credit!

Each of these parts can be tested with a provided set of **non-exhaustive** unit tests. That means that if your code fails the tests, it is definitely wrong. The converse is not true: code that can pass the tests is not necessarily correct code.  **Passing the unit tests is not enough to ensure a good score**. You should test your code significantly more on your own. Do not attempt to hard code, corner-case, or otherwise overfit your solution to the tests. We have provided you a subset of the tests that we will use for grading.


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

