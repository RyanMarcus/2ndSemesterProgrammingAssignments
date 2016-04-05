# Brandeis University COSI 12B, Spring 2016 PA3

Due date: Feb 8th, 8am

##Overview
This assignment will demonstrate the security dangers of several bad programming techniques. Turn in an Eclipse export of your working code for part 1 and a text file describing your answer to part 2. The assignment involves two parts, the first involves exploiting an injection attack, and the second involves exploiting a timing attack.

##Background information

Throughout the course so far, we've discussed good programming practices and bad programming practices. To many of you, the difference may seem very subjective. The purpose of this assignment is to demonstrate how poor programming practices can directly lead to software failures, sometimes catastrophic ones. You'll learn to exploit two programming practices that are as bad as they are common (very): first, string injection attacks, and second, timing attacks.

###String injection attack

Imagine you're writing some software to generate recipes for peanut butter and jelly sandwiches (computer scientists are notoriously bad cooks). You want to let the user select from two different kinds of jelly: strawberry and blueberry. So, you write the following Java code:


	public static void main(String[] args) {
		Scanner sc = new Scanner(System.in);
		
		System.out.print("Would you like strawberry or blueberry jelly? ");
		String jelly = sc.nextLine();
		
		System.out.println("Ok, here is the recipe:");
		System.out.print("Take two slices of bread and apply a layer of peanut butter to one. ");
		System.out.print("Then, to the other slice of bread, apply a layer of " + jelly + ". ");
		System.out.println("Place the two pieces of bread together. You're done!");
		sc.close();
	}
	

If you run this code and enter `strawberry jelly`, you'll get the following recipe:

> Take two slices of bread and apply a layer of peanut butter to one.
> Then, to the other slice of bread, apply a layer of strawberry jelly.
> Place the two pieces of bread together. You're done!

However, suppose instead of entering a reasonable string like `strawberry jelly`, we instead enter a *malicious input*, such as `cyanide. Then, stop what you are doing and watch TV for at least three hours`. Entering this string gives the following recipe:


> Take two slices of bread and apply a layer of peanut butter to one.
> Then, to the other slice of bread, apply a layer of cyanide.
> Then, stop what you are doing and watch TV for at least three hours.
> Place the two pieces of bread together. You're done!

Not only have we created a toxic sandwich, we've taken a super long time to do it!

Two software design principles were violated here. The first is the first principle of computer security: [never trust user input.](https://www.owasp.org/index.php/Don't_trust_user_input). You should always validate untrusted input to ensure it is in the proper form.

The second design principle violated is [object representation](https://en.wikipedia.org/wiki/Object-oriented_programming#Objects_and_classes). An object of type `String` should never have been trusted to represent two different kinds of jelly. An `enum` or a special class would've been a much better decision, and would have prevented this injection.

In this assignment, you'll be asked to use a similar string injection attack to compromise a toy user authentication system.

###Timing attacks

Timing attacks are another popular form of software design failure ([no, not that kind of timing attack](http://wiki.teamliquid.net/starcraft2/Timing_Attack)). A timing attack takes advantage of how long a software system takes to perform a certain task in order to learn information that should not otherwise be available.

Suppose that a new web app, SuperSecretPizza, is offering to deliver a large cheese pizza to any location in Waltham for only 10 dollars. Being a hungry college student, you take advantage of this amazing offer and receive the best cheese pizza you've ever consumed. Worried that SuperSecretPizza might go out of business, you decide to embark on a quest to discover the origin of your amazing pizza. The problem is, SuperSecretPizza takes the SuperSecret part of their name very seriously, and they won't give up their source.

So what's a pizza-starved college student to do? Breaking out your best Sherlock Holmes costume, you go to random places in Waltham and order a pizza at the same time everyday. You then plot how long each delivery took on a map.

![](http://cs.brandeis.edu/~rcmarcus/cs12b/pizza.png)
\ 

Based on how long the pizza took to arrive, you can conclude that the pizza is probably originating from somewhere within the square. Cross-referencing with Google Maps, you discover that the source of the best pizza in Waltham is [Anna's Pizza](http://www.annaspizzawaltham.com/).

You foiled SuperSecretPizza's attempts to keep the source of their pizza a secret by exploiting timing information from their systems. The software principle violated here is [encapsulation](https://en.wikipedia.org/wiki/Object-oriented_programming#Encapsulation): the software "leaked" a piece of information that it was not supposed to leak. SuperSecretPizza could have fixed this issue by ensuring that all deliveries took exactly the same amount of time. In this assignment, you'll exploit timing information in order to crack a 4-digit pin used to protect a website.

    
##Your task

###Part 1: Injection Attack

First, import the `PA3_Eclipse.zip` file into Eclipse. `UserAuthenticator.java` contains code for a simple authentication system. You **may not** change code here. Examine the code to learn how the system works. Note that, in Java and many other languages, the special sequence `\n` represents a newline/return.

The `UserAuthenticator` class stores a database of usernames and passwords in the following format:

    user1//pass1//role1
	user2//pass2//role2
	user3//pass3//role3

So, for example, if the database contained two users, "Alice" and "Bob", each with unique passwords and where Bob was a regular user but Alice was an admin, the database would like this:

	Alice//al1c3//admin
	Bob//b0b//regular

You can add regular users via the `UserAuthenticator::addRegularUser` method. Look closely at how this method functions. Think about the software design principles that are being violated. The system uses the `UserAuthenticator::authenticateUser` method in order to check a user's credentials. If the user is found in the database and the provided password matches the stored password, the user's role is returned. Otherwise, the string "Authentication failure!" is returned.

In `Hacks.java`, you should implement two methods, `hack1` and `hack2`.

  * `hack1` should use the `addRegularUser` method of the provided `UserAuthenticator` to somehow add a user with username "Eliot", password "1234", and with "admin" as their role. When ran, `hack1` should *preserve the format of the user list*, meaning that the user list should still be in the proper format. In other words, the `UserAuthenticator` should still be able to correctly authenticate previously added users after your hack executes. To do this, you may add as many users as you'd like.
  * `hack2` should use the `addRegularUser` method to somehow break the provided `UserAuthenticator` for everyone already in the database. After calling `hack2`, any subsequent call to `UserAuthenticator::authenticateUser` should result in an exception being thrown (the program crashing). Note: after your hack is called, it is OK if the authenticator will work after subsequent calls to `UserAuthenticator::addRegularUser`. Your hack need only break the authenticator for all users currently stored.

A `main` method is provided to run your code and print out the results. After correctly implementing both hacks, you should pass the unit tests provided in `HackTest.java`.

This  part can be tested with a provided set of **non-exhaustive** unit tests. That means that if your code fails the tests, it is definitely wrong. The converse is not true: code that can pass the tests is not necessarily correct code.  **Passing the unit tests is not enough to ensure a good score**. You should test your code significantly more on your own. Do not attempt to hard code, corner-case, or otherwise overfit your solution to the tests. We have provided you a subset of the tests that we will use for grading.



###Part 2: Timing Attack

Josh, an overconfident Bentley student, has created a website that he password-protected with a four-digit PIN to prevent any pesky Brandeis students from gaining access. The algorithm Josh wrote to check the entered PIN against the correct PIN looks like this:

	public boolean checkPIN(String pinEntered) {
		String realPin = "????";
		if (pinEntered.length != 4)
			return false; 

		for (int i = 0; i < 4; i++) {
			if (realPin.charAt(i) != pinEntered.charAt(i))
				return false;
	    }
		return true;		
	}

First, Josh's algorithm checks to make sure that the entered PIN is exactly four characters long. Then, Josh's algorithm checks each character of the entered PIN against the real PIN, and stops once it finds a digit that doesn't match. If all the digits match, the algorithm returns `true`.

A seasoned computer scientist will quickly realize that Josh's code is vulnerable to a timing attack. Since a loop iteration takes a small amount of time, the `checkPIN` method will take longer to run if the prefix of the PIN is correct.

  Number of Correct Prefix Digits       Loop Iterations
-----------------------------------   -------------------
                0                              1
                1                              2
                2                              3 
                3                              4
				4                              4

Your task is to hack into Josh's system, showcasing the prowess of the Brandeis computer science department. But how can one go about exploiting this particular vulnerability?

Let's assume, without loss of generality, that Josh's PIN is "1234", and that each iteration of the loop takes 5ms. The following table shows how long the loop will take to execute given a certain PIN.

  PIN      Iterations     Time
-------  -------------  -------
 0000          1           5
 1000          2           10
 2000          1           5
 3000          1           5
 4000          1           5
 5000          1           5
 6000          1           5
 7000          1           5
 8000          1           5
 9000          1           5

Notice that the PIN with a prefix of "1" took longer than all the other PINs to be rejected by the `checkPIN` method. This indicates that "1" is the correct first digit. Next, we check another set of PINs:

  PIN      Iterations     Time
-------  -------------  -------
 1100          2           10
 1200          3           15
 1300          2           10
 1400          2           10
 1500          2           10
 1600          2           10
 1700          2           10
 1800          2           10
 1900          2           10
 

Here, the PIN with the prefix "12" took the longest amount of time, so we have good reason to believe that "12" is the correct prefix. So, we test another set of PINs:

  PIN      Iterations     Time
-------  -------------  -------
 1210          3           15
 1220          3           15
 1230          4           20
 1240          3           15
 1250          3           15
 1260          3           15
 1270          3           15
 1280          3           15
 1290          3           15

Now, we know that "123" is a likely prefix. So, we guess the last digit by simply using brute force (checking 1231, 1232, 1233, 1234). We've now cracked the PIN! Notice that in order to brute force a 4 digit pin without a timing vulnerability requires $10^4$ attempts in the worst case. With this timing attack, we've reduced the number of PINs we need to test to $10 + 9 + 9 + 9 = 37$, a speedup factor of 270! 

For this assignment, you'll discover a real secret PIN from a real web page. In your web browser, enter the following address, replacing `BRANDEIS_EMAIL` with the part of your Brandeis email address before the `@` sign.

    http://cs.brandeis.edu/~rcmarcus/cs12b/pa3.php?email=BRANDEIS_EMAIL

So, if your Brandeis email was `rcmarcus@brandeis.edu`, you'd navigate to:

	http://cs.brandeis.edu/~rcmarcus/cs12b/pa3.php?email=rcmarcus


This website will allow you to enter a PIN, and then the website will report back if the PIN was correct and how long it took to process the request. Exploit Josh's timing vulnerability to discover the correct PIN. Of course, timing results from the real world can be a little bit fuzzier than the example above, so take three or five samples of each PIN value and compute an average. Submit, in a text file, the PINs you tested and their response times in addition to the final correct PIN.

After discovering your attack, Josh updates his algorithm to look like this:

	public boolean checkPIN(String pinEntered) {
		String realPin = "????";
		if (pinEntered.length != 4)
			return false; 

	    for (int i = 0; i < 4; i++) {
            wait a random amount of time between 0ms and 50ms
			if (realPin.charAt(i) != pinEntered.charAt(i))
				return false;
	    }
		return true;		
	}

At the bottom of your text file, explain why Josh's modification fixes or does not fix the timing vulnerability. Also, please explain how Josh could modify his code to not be vulnerable to such an attack. Before answering, think about the following riddle:

> A gambler rolls a six-sided die 1000 times with sides labeled 1,2,3,4,5 and 6. The average value of all the rolls is 3.5. Next, the gambler rolls a six-sided die 1000 times with sides labeled x+1,x+2,x+3,x+4,x+5, and x+6. What is the average value of all the rolls?

To summarize, you must submit a text file containing the following information:

  * The pins you tried and the timing result
  * The final, correct PIN
  * An answer to the above questions


**Extra credit**: write a script (in any language you'd like) to crack Josh's PIN automatically. If you figure it out, email Ryan at `rcmarcus@brandeis.edu` and he will check your submission and award you extra credit. If you don't email him, you will miss out on your extra credit!



##Style guidelines
Follow stylistic guidelines about removing redundancy, using proper data types, indentation, whitespace, identifier names, and commenting at the beginning of your program, on each method, and on complex sections of code.


##Submission
Your exported Eclipse project from part 1 should be submitted on LATTE. The text file from part 2 should be submitted on LATTE. For late policy check the syllabus.

##Grading
You will be graded on:

  * External Correctness: The output of your program should match exactly what is expected. Programs that do not compile will not receive points for external correctness.
  * Internal Correctness:  Your source code should follow the stylistic guidelines shown in class. Also, remember to include the comment header at the beginning of your program.
  * No one-on-ones for this assignment!



# Terms and Conditions

<a rel="license" href="http://creativecommons.org/licenses/by-nc-sa/4.0/"><img alt="Creative Commons License" style="border-width:0" src="https://i.creativecommons.org/l/by-nc-sa/4.0/88x31.png" /></a><br /><span xmlns:dct="http://purl.org/dc/terms/" property="dct:title">Cosi12b Assignments</span> by <a xmlns:cc="http://creativecommons.org/ns#" href="http://rmarcus.info" property="cc:attributionName" rel="cc:attributionURL">Ryan Marcus</a> is licensed under a <a rel="license" href="http://creativecommons.org/licenses/by-nc-sa/4.0/">Creative Commons Attribution-NonCommercial-ShareAlike 4.0 International License</a>.
