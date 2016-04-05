# Brandeis University COSI 12B, Spring 2016 PA8

Due date: March 21st, 2016

Leaderboard link: [https://rmarcus.info/lb](https://rmarcus.info/lb)

##Overview
This assignment will teach you about data compression and humanity's impractical indifference to flagrant competition. Turn in an Eclipse export of your working code. The assignment involves implementing code to compute the shortest route between two points.

## Background information

The field of data compression actually pre-dates modern computer science. The last major leap in the field was made by [Claude Shannon](https://en.wikipedia.org/wiki/Claude_Shannon), who is often thought of as the "father of information theory." Shannon discovered a formula to compute the best possible compression rate for a particular piece of data, known has an information's [Shannon entropy](https://en.wikipedia.org/wiki/Entropy_(information_theory)).

We won't concern ourselves with precise mathematics (phew), but we can consider a few common information compression techniques that are both simple and useful. We recommend that you consider each of these approaches during the assignment, and research more on your own as well.

As computer scientists, we should care about how we *measure* compression. Luckily, loseless compression is a pretty easy thing to measure. If I give you two working compression schemes, `A` and `B`, and ask you to figure out which one is better at compressing a particular file, you could pass that file through both schemes and see which one produced the smaller file. Generally, we say that the *compression ratio* of a particular compression program is the size of the original file divided by the size of compressed file. So, if the original file was 1GB and the compressed version was 0.5GB, we would say that the compression ratio is:

$$\frac{1 \mbox{ GB}}{0.5 \mbox{ GB}} = 2$$

Another way to read the compression ratio is the factor that the original file was reduced by. Here, we say that the original file was reduced by a factor of two (cut in half). In general, higher compression ratios are better. In this assignment, you will be graded based on the compression ratio you achieve. 

### Run-length encoding

Imagine you are flipping a weighted coin that has a 99% chance of landing on heads and a 1% chance of landing on tails. You observe the following sequence of flips:

> H H H H T H H H H H H H H H H T H H

Now, you could choose to store the coin flips just like that, using a total of 18 characters. Alternatively, we could use *run-length encoding* to keep track of the number of `H`s we observe in a row, and encode the sequence as follows:

> 4H 1T 10H T 2H

... which uses just 10 characters, compressing the data significantly. Formally, the idea is to replace each token of input with a tuple `(count, token)`, that expresses the token and the number of times the token was repeated. When data contains long runs of similar data, this can be very useful.


### Fixed-width delimiters

In software systems, we often store information as *comma-separated values*, or CSV. Consider this CSV data which expresses information about street addresses:

> 40 Brown St,Waltham,02453,MA  
> 182 Robbins St,Waltham,02453,MA  
> 450 Bryce Ave,Los Alamos,87544,NM  
> 1120 Beantree Lane,Tucson,85745,AZ  

The first field is the street address, the second field is the city, the third field is the ZIP code, and the last field is the state. Notice, however, that ZIP codes are *always* 5 digits, and that state codes are *always* two letters. In these cases, we no longer need to store the comma between two values with known length. We can instead store:

> 40 Brown St,Waltham,02453MA  
> 182 Robbins St,Waltham,02453MA  
> 450 Bryce Ave,Los Alamos,87544NM  
> 1120 Beantree Lane,Tucson,85745AZ  
  
... which saves us one comma per-line. 

### Huffman encoding

Imagine that you are trying to come up with a binary (zeroes and ones) encoding for four different letters: `A, B, C, D`.

Now, we could use a very simple, two-bit encoding:

> A -> 00  
> B -> 01  
> C -> 10  
> D -> 11  

In this way, each character is represented by exactly two bits. For the purposes of practice, we can calculate the average number of bits needed for each character, assuming that each of the four characters are equally represented.

$$\frac{1}{4} * 2 + \frac{1}{4} * 2 + \frac{1}{4} * 2 + \frac{1}{4} * 2 = 2$$


If you know that `A`, `B`, `C`, and `D` are all represented in the data equally, then this is probably a pretty good idea.

However, let's pretend that our data is composed of 91% `A`s, 3% `B`s, 3% `C`s, and 3% `D`s. Now we have a lot of `A`s, and very few `D`s, but we are using the same number of bits to represent both of them! Again, we can calculate the average number of bits needed to represent each character just like we did before:

$$\frac{91}{100} * 2 + \frac{3}{100} * 2 + \frac{3}{100} * 2 + \frac{3}{100} * 2 = 2$$


Again, still 2 bits on average. Instead, imagine we used the following encoding:

> A -> 0  
> B -> 10  
> C -> 110  
> D -> 111  

Now, `A` is represented with fewer bits, `B` is represented with the same number of bits, but `C` and `D` use an extra bit. We can use our equation from above to calculate how many bits we need per-character on average:

$$\frac{91}{100} * 1 + \frac{3}{100} * 2 + \frac{3}{100} * 3 + \frac{3}{100} * 3 = \frac{115}{100} = 1.15$$

Now, on average, we are using only 1.15 bits per character instead of two! That's a pretty massive improvement. Of course, the devil is in the details: you can read about how to find such encodings [on Wikipedia](https://en.wikipedia.org/wiki/Huffman_coding#Basic_technique), but you'll probably be better off letting a library take care of it for you. More on that later.


### Functional-dependency driven decomposition

Consider again the CSV data from above:

> 40 Brown St,Waltham,02453,MA  
> 182 Robbins St,Waltham,02453,MA  
> 450 Bryce Ave,Los Alamos,87544,NM  
> 1120 Beantree Lane,Tucson,85745,AZ

This data contains *redundant information*. Specifically, we some *domain knowledge*, we can infer the state from the ZIP code. The ZIP code `87544` will always be followed by `NM`, since that ZIP code is entirely in New Mexico. Thus, we can remove the state abbreviation entirely, and simply lookup the ZIP code when we decompress the data. We say that there is a *functional dependency* between the ZIP code and the state, or that the ZIP code *determines* the state, to express this relationship.

### Column vs. row storage

Consider our example again:

> 40 Brown St,Waltham,02453,MA  
> 182 Robbins St,Waltham,02453,MA  
> 450 Bryce Ave,Los Alamos,87544,NM  
> 1120 Beantree Lane,Tucson,85745,AZ

Here, we show the example stored in *row major* order: each row of the data is represented on a line. Imagine instead that we transposed the dataset, and viewed the data in *column major* order.

> 40 Brown St, 182 Robbins St, 450 Bryce Ave, 1120 Beantree Lane  
> Waltham, Waltham, Los Alamos, Tucson  
> 02453, 02453, 87544, 85745  
> MA, MA, NM, AZ  

Organizing the data in this way doesn't change the length of the data, but it does provide more opportunities for using run-length encoding, fixed delimited encoding, and Huffman encoding, because now each line of the file can be encoded differently. For example, for the states and ZIP codes, all commas can be removed. For the cities, run-length encoding can now be used.


### Helpful Java classes

Java has some built in compression tools. Take a look at them:

[https://docs.oracle.com/javase/8/docs/api/java/util/zip/Deflater.html](https://docs.oracle.com/javase/8/docs/api/java/util/zip/Deflater.html)

[https://docs.oracle.com/javase/8/docs/api/java/util/zip/Inflater.html](https://docs.oracle.com/javase/8/docs/api/java/util/zip/Inflater.html)

Using them alone, you can probably achieve a pretty good score. Combining them with the techniques presented above will likely prove most fruitful.

There's also special classes that extend `InputStream` and `OutputStream` that automatically handle data compression for you. They are [`DeflaterOutputStream`](https://docs.oracle.com/javase/8/docs/api/java/util/zip/DeflaterOutputStream.html) and [`InflaterInputStream`](https://docs.oracle.com/javase/8/docs/api/java/util/zip/InflaterInputStream.html).

**"Help! I used the Deflater and Inflater but got a really low compresison ratio -- only 1.01! What's wrong?"** Pay careful attention to the number of bytes you are passing to the `Deflater` and the number of bytes you are then writing to the file. Are you writing the same number of bytes as you are *passing* to the `Deflater`? Then you've written the same number of bytes as the original file! Pay attention to the return value of `Deflater.deflate`...

## Your task

Your task will be to write code that takes in an array of *orders* and writes them out to a file. Then, you'll need to write code that will read in that file and reconstruct the array of orders.

Orders look like this:

```
Brandon Gould|Metzger's Hardware|143|768551|999060|76253|TX
Christopher Goddard|Amazon|1|385276|274207|65018|MO
```

The columns represent the following information:

  1. Who is the buyer?
  2. Who is the seller?
  3. What is the quantity of the order?
  4. What is the SKU (ID number) of the item being ordered?
  5. What is the price (per unit) of the item, in cents?
  6. What is the ZIP code of the customer?
  7. What state is the customer in?

So, the first line above represents that Brandon Gould ordered 143 items with SKU 768551 from Metzger's Hardware, and that Brandon's ZIP code is 76253, that Brandon lives in Texas, and that these particular items cost 9990.60 each.

One could parse a single line of the input:

    Brandon Gould|Metzger's Hardware|143|768551|999060|76253|TX

... using the following Java code:


```java
// you would be reading this from a file...
String s = "Brandon Gould|Metzger's Hardware|143|768551|999060|76253|TX";
        
// split on the | character, which is a special character,
// so we put a \\ before it.
String[] fields = s.split("\\|");
        
System.out.println(fields[2]); // prints "143"
```


Your assignment is to complete the `Compress` and  `Decompress` classes. This means at least writing the `compress` and `decompress` methods (because they are abstract in the parent class).

Good class design includes brief classes and even briefer methods. This is accomplished by the “single responsibility principle” which says that each class and each method should be focused on one thing. As you think about this, you will realize that judging whether there’s a single responsibility is a subtle and subjective thing. In situations like this, rules of thumb come in handy. You should follow the guidance that a class should not be more than 100 lines of code, and a method not more than 15 lines of code.

The `compress` method of the `Compress` class is  given an array of orders, where each `String` in the array represents a line of the input (separated by bars/pipes). You will also be given a filename. You should write out the array of orders, compressed, into a file with the given name.


In order to write the array out to a file, fill in the `compress` method of the `Compress.java` file. Here, you will be given an array of orders, where each `String` in the array represents a line of the input (separated by bars/pipes). You will also be given a filename. You should write out the array of orders, compressed, into a file with the given name.

In order to reconstruct the array from the file, fill in the `decompress` method of the `Decompress.java` file. This method takes in a single parameter representing the file you should read in, and this method must return the reconstructed array.

Your `compress` method will be expected to write out to a file, and your `decompress` method will be expected to reconstruct the array from such a file.

Once you have some code written, you can run the unit tests to make sure that your code is properly reconstructing the array. 

**Important note: you do not need to keep the array in order. You may move elements around, but each element of the array must be exactly preserved. Think about how you could use this to take advantage of run-length encoding.**


Once you are properly reconstructing your array, you can test your code by submitting to the leaderboard. To do this, open up `Leaderboard.java` and modify the constants:

  * Make sure that `EMAIL_ADDRESS` is set to your Brandeis email address.
  * Make sure that `NICKNAME` is set to something you can recognize. It will appear publicly on the leaderboard. You can use your real name if you'd like, but you may also use a fake name if you'd like to be anonymous. You may *not* use another student's real name.

After setting these two variables, press the run button. This program will calculate how good your compression is and post the result to the leaderboard. You can view the leaderboard here: [https://rmarcus.info/lb/](https://rmarcus.info/lb/).


For your final grade, we will test your code on a different, but similar, input array. Be careful not to make assumptions about the data that might not hold everywhere.

## Restrictions and important notes

  1. **No cheating.** You should faithfully compress and decompress the information passed. Do not copy/paste the test file into  your decompress method and simply return it. We will test your code using a different text file, and anyone found to be "special casing" the leaderboard will get a zero.
  2. **Be respectful.** Competition can be fun, but it can also be stressful. We aren't in 4th grade anymore, so I shouldn't have to say this, but do not brag or belittle others about their scores, be they low or high. Respect your peers. Being at the top can change quickly.
  3. **Don't make assumptions about the data.** You may assume that all ZIP code and state combinations are valid, but you may not assume that there are only a certain number of buyers, sellers, etc. You may assume that all ZIPs are 5 digits and that all SKUs are 6 digits. If you want to make additional assumptions about the data, ask first.

## Getting started guide
If all of this seems overwhelming, here's a few simple steps to get you started:

**First, just write the array to a file and read it back in.**

Don't worry about doing any kind of compression yet. Just get things written out in such a way that you can read them back in. Here's an example of how to do this with `ObjectOutputStream` and `ObjectInputStream`, two special stream types that can write out entire objects:

```java
import java.io.*;
import java.util.*;

class Main {
  public static void main(String[] args) {
    
    try {
      ObjectOutputStream oos = new ObjectOutputStream(new FileOutputStream(new File("test.txt")));
    
        String[] a = new String[] {"this", "is", "a", "test"};
    
        oos.writeObject(a); 
        oos.close();
    } catch (Exception e) {
        // something went wrong!
        e.printStackTrace();
    }



    // now, let's read the array back in...
    
    try {
        ObjectInputStream ois = new ObjectInputStream(new FileInputStream(new File("test.txt")));
        
        String[] a = (String[]) ois.readObject();
        
        System.out.println(Arrays.toString(a));
        
        ois.close();
    } catch (Exception e) {
        // something went wrong!
        e.printStackTrace();
    }

  }
}
```

`Object[Output|Input]Stream` is very easy to use, but it also adds a lot of extra information that you might not need. Thus, instead of using an `ObjectOutputStream`, you might want to use a `DataOutputStream` instead. Read.

Once you can write an array to a file and recover it, you should pass the unit tests and you should make a submission to the leaderboard to make sure you aren't doing anything that won't scale to a larger file (like concatenating strings).



**Second, add an DeflaterOutputStream and a InflaterInputStream to your chain.**

As you've learned, IO streams in Java are composable. Previously, we put an `ObjectOutputStream` on top of a `FileOutputStream`. Well, you can also put an `ObjectOutputStream` on top of a `DeflaterOutputStream` on top of a `FileOutputStream`. You can do the same with input streams.

Simply add the `DeflaterOutputStream` and `InflaterInputStream` to your chain, re-run the unit tests, and resubmit to the leaderboard. Obverse your higher score.

**Third, experiment with other ways to compress the data.**

Now, go back to the top of the assignment specification and try some different encoding techniques. One of the best ways to get additional mileage out of the `DeflaterOutputStream` is to sort the data on a low-entropy field (a field without many different values), and then use run length encoding.




##Style guidelines
Follow stylistic guidelines about removing redundancy, using proper data types, indentation, whitespace, identifier names, and commenting at the beginning of your program, on each method, and on complex sections of code.


##Submission
Your exported Eclipse project should be submitted on LATTE, and you should make sure to submit to the leaderboard. For late policy check the syllabus.

##Grading


Your assignment will be graded in two halfs:

  1. Performance (50 points): this will be based on how well your code does relative to a watermark and your peers
  2. Style (50 points): this will be based on your 1-1 TA's ability to understand your code, and your adherence to good design principles like the single responsibility principle.

###Performance

This half is split into a 20% "watermark" and a 30% "rank." You get 20% of the performance grade for writing a solution with a compression ratio better than 1.75. The "rank" portion of the grade will be distributed based on your rank within the course. The top student will get 30/30 points. Other students will get a number of points scaled linearly with their rank, determined by this formula, where `r` is [your rank on the leaderboard](https://rmarcus.info/lb/):

$$27.304 * \left(1 - \frac{r-1}{90}\right)$$

If you do not correctly reconstruct the input of the provided test set and a few others we have not shown, you will not get any points for performance. Further, if your code as *any* compile errors, you will not get any points for performance. 

To summarize:

  * 20 possible points for having a compression ratio better than 1.75
  * 30 possible points from your rank
  * No points if your code can't reconstruct the input or has errors


### Code style
As usual, half of your style grade will come from simply attending your 1-1 on time. The other half will come from your TA's ability to read and understand your code, and your adherence to good software design principles like those discussed in class (e.g., single responsibility principle).

As we've now progressed reasonably far in the course, we will no longer tolerate extremely long methods, or putting all of your code into a single class.


# Terms and Conditions

<a rel="license" href="http://creativecommons.org/licenses/by-nc-sa/4.0/"><img alt="Creative Commons License" style="border-width:0" src="https://i.creativecommons.org/l/by-nc-sa/4.0/88x31.png" /></a><br /><span xmlns:dct="http://purl.org/dc/terms/" property="dct:title">Cosi12b Assignments</span> by <a xmlns:cc="http://creativecommons.org/ns#" href="http://rmarcus.info" property="cc:attributionName" rel="cc:attributionURL">Ryan Marcus</a> is licensed under a <a rel="license" href="http://creativecommons.org/licenses/by-nc-sa/4.0/">Creative Commons Attribution-NonCommercial-ShareAlike 4.0 International License</a>.
