# Brandeis University COSI 12B, Spring 2016 PA6

Due date: March 7th, 2016

##Overview
This assignment will give you practice connecting APIs. Turn in an Eclipse export of your working code. The assignment involves implementing code to compute the shortest route between two points.

##Background information
### APIs

An [API, or **a**pplication **p**rogramming **i**nterface](https://en.wikipedia.org/wiki/Application_programming_interface), is a colleciton of functionalities provided by a service to programmers. You can think of an API as simply as a library of Java code to do something. In this specific case, the libraries actually perform their work by asking, for example, something from Twitter. You don't need to know how it does that (although if you're curious we can explain it.) But initially you can just *believe* that it will :)

In this assignment, you'll be using  different APIs in order to make word clouds from Twitter about a particular topic.

  1. [Hosebird, a Java API for interacting with Twitter](https://github.com/twitter/hbc) that will allow you to pull down recent tweets containing certain terms. In other words it will (behind the scenes) talk to twitter.com and bring back to you tweets based on your criteria.
  2. [Apache Lucene, a Java search and indexing service](https://lucene.apache.org/) that provides a lot of low-level tools for analyzing natural language. We won't be using Lucene's main functionality -- generating search engines -- but we will use a few tools that Lucene provides.
  3. [Kumo, a Java API for generating word clouds](https://github.com/kennycason/kumo) that will let you easily create word clouds.
  4. [JSON.org's JSON parser](http://www.json.org/). JSON is a text format that is used very commonly on the web to exchange data. In other words, its just a way to format a text file so that someone else can interpret it. This JSON parser will make it easy to process what you get from Twitter through Hosebird API.

## Your task

Your task is to generate word clouds that look like this:

![A word cloud](http://cs.brandeis.edu/~rcmarcus/cs12b/cloud.png)
\

In the `TwitterCloud` class, you should write a method called `makeCloud` that takes as parameters an array of terms to search Twitter for and a filename to which you should write the word cloud.

```
public void makeCloud(String[] terms, String filename);
```

So, if `makeCloud` is called with the array `['donald', 'trump']` and the filename `test.png`, you should search Twitter for the terms `donald` and `trump` and write a word cloud of the results to `test.png`.

Sounds simple, right? But doing this will be a bit of an involved process. Here's a little

### Step 0: Maven

[Apache Maven](https://maven.apache.org/) is a free (as in speech) tool that youc an use to manage your program's dependencies. You've actually been using it for all the previous programming assignments without knowing it. Since Java doesn't include things like the Twitter API by default, we have to add them to a special file called `pom.xml`.

You should see such a file in your PA after you import into Eclipse. If you double click on it, Eclipse will open up the file in a special mode for looking at Maven files. We don't need any of Eclipse's fancy features, we just want to edit the `pom.xml` directly. To do so, select the "pom.xml" (*not* the "Effective POM") tab at the bottom of the editor window.

Here, you should see a file in [XML format](https://en.wikipedia.org/wiki/XML). (Not to confuse you but it's interesting to note that XML is a format analogous to JSON, a standard syntax for a structured text file. If you know HTML you will notice that it's analogous.)

However, you can safely follow these instructions without delving into XML at all. In order to add the dependencies we need for this project, add this block of code right before the ending `project` tag, which looks like `</project>`:

```
<dependencies>
 <dependency>
	<groupId>junit</groupId>
	<artifactId>junit</artifactId>
	<version>4.11</version>
	<scope>test</scope>
 </dependency>


 <dependency>
	<groupId>org.json</groupId>
	<artifactId>json</artifactId>
	<version>20151123</version>
 </dependency>

 <dependency>
	<groupId>com.twitter</groupId>
	<artifactId>hbc-core</artifactId>
	<version>2.2.0</version>
 </dependency>


 <dependency>
	<groupId>org.apache.lucene</groupId>
	<artifactId>lucene-core</artifactId>
	<version>5.4.1</version>
 </dependency>

 <dependency>
	<groupId>org.apache.lucene</groupId>
	<artifactId>lucene-analyzers-common</artifactId>
	<version>5.4.1</version>
 </dependency>


 <dependency>
	<groupId>com.kennycason</groupId>
	<artifactId>kumo</artifactId>
	<version>1.4</version>
 </dependency>
</dependencies>
```

(we've done this for you already, as you can see by looking at the file)

After adding the above text, save the file, and then give Eclipse a few seconds to automatically fetch those dependencies. That's it! Instead of having to go and download each of those independently, add them to your project, and make sure you have the right version, you can simply specify some information in your `pom.xml` and let Maven take care of the rest.

You can now close the `pom.xml` file.

### Step 1: The Twitter API

The first thing you should do is register for a Twitter account, if you don't already have one. After that, go to [https://apps.twitter.com/](https://apps.twitter.com/) and create a new application. Fill out the forms accurately and note four pieces of information: your *consumer key*, your *consumer secret*, your *token*, and your *token secret*.

The following code provides a basic example for getting started with Hosebird.

```
// Configure Twitter "hosebird" (derived from "fire hose" and bird which is the animal that Tweets)
Hosts hosebirdHosts = new HttpHosts(Constants.STREAM_HOST);
StatusesFilterEndpoint hosebirdEndpoint = new StatusesFilterEndpoint();

List<String> terms = Arrays.asList(args);

hosebirdEndpoint.trackTerms(terms);

Authentication hosebirdAuth = new OAuth1(CONSUMER_KEY,
		CONSUMER_SECRET, TOKEN, TOKEN_SECRET);


BlockingQueue<String> msgQueue = new LinkedBlockingQueue<String>(100000);

// Connect to service and start watching for the terms of interest
ClientBuilder builder = new ClientBuilder()
		.hosts(hosebirdHosts)
		.authentication(hosebirdAuth)
		.endpoint(hosebirdEndpoint)
		.processor(new StringDelimitedProcessor(msgQueue));

Client hosebirdClient = builder.build();
hosebirdClient.connect();


List<String> tokens = new LinkedList<String>();
while (tokens.size() < NUMBER_TOKENS) {
	String msg = msgQueue.take();
    // do something with msg here!
}

hosebirdClient.stop();
```

Study this code and the [hosebird documentation](https://github.com/twitter/hbc) to ensure you understand what it does. At each iteration of the loop, `msg` will be populated with a [JSON](https://en.wikipedia.org/wiki/JSON) representation of tweets. Initially, you should probably print it out and figure out which fields you might want to extract. Afterwards, you should use a [JSONObject](https://sling.apache.org/apidocs/sling5/org/apache/sling/commons/json/JSONObject.html) in order to parse the JSON. **Do not parse the JSON response using normal Java string methods like `substring` or `split`.**


You **must not store your consumer secret and token secret directly inside of your Java code.** Instead, use [environmental variables](https://en.wikipedia.org/wiki/Environment_variable) in order to store these values. You can retrieve an environmental variable inside of Java code by using the [`System.getenv(String)` method](https://docs.oracle.com/javase/8/docs/api/java/lang/System.html#getenv-java.lang.String-), and you can set environmental variables inside Eclipse using the run configurations dialog, [as explained here](http://stackoverflow.com/a/12810433).

Once you've extracted the contents of the tweet, you can go to the next step...

### Step 2: Tokenizing and Stemming

In natural language processing, two popular tasks called *tokenization* and *stemming* are often employed. [Tokenization](https://en.wikipedia.org/wiki/Tokenization_(lexical_analysis)) refers to splitting up a sentence or paragraph into *tokens*. For example, given the sentence:

> Software-as-a-service systems are often co-located with traditional hardware.

... might be tokenized as follows:

> Software | as | a | service | systems | are | often | co-located | with | traditional | hardware

Notice that "Software-as-a-service" is split into four tokens (because each word should be evaluated independently), but "co-located" is kept as a single token, as "co" is meaningless by itself. Tokenization is thus a seemingly simple, but actually quite complex, task. Luckily, the Apache Lucene project has provided us with a high-quality tokenizer we can use in our program.


Stemming is the process of taking a single token and reducing it to a stem. For example, the token `reducing` and `reduce` would both stemmed to `reduc`. This is an important step because it will take words with almost identical meaning but different suffixes and turn them into the same word. We want our word cloud to count words like "lie", "lying", and "liar" as the same word in order to visualize things correctly.

Here's how to use Lucene to turn a `String` into a `List` of stems, combining the tokenization and stemming process:

```
List<String> tokens = new LinkedList<String>();
try (EnglishAnalyzer an = new EnglishAnalyzer()) {
	TokenStream sf = an.tokenStream(null, tweet);

	try {
		sf.reset();
		while (sf.incrementToken()) {
			CharTermAttribute cta = sf.getAttribute(CharTermAttribute.class);
			tokens.add(cta.toString());
		}
	} catch (Exception e) {
		System.err.println("Could not tokenize string: " + e);
	}
}
```

Now, you may or may not want to turn the stems into a list, but the idea is the same.

This code makes use of the [Lucene `EnglishAnalyzer`](https://lucene.apache.org/core/5_2_0/analyzers-common/org/apache/lucene/analysis/en/EnglishAnalyzer.html) in order to use a powerful tokenizer and stemmer for the English language.

The second line uses a pattern called the ["try-with-resources" statement](https://docs.oracle.com/javase/tutorial/essential/exceptions/tryResourceClose.html). Remember to design your code to be modular and make good use of classes, objects, etc.


You should use the `EnglishAnalyzer` class to make your program loop until it has collected the correct number of unique stemmed tokens.

### Step 3: Building a frequency map and using Kumo

Once you've written code to collect the correct number of stemmed tokens from Twitter, you should build your data into a frequency map. Regardless of how you collected the tokens from the tweets, you should next create a `Map<String, Integer>` which maps each token to the number of times you observed it.

Then, you should convert your map into a `List<WordFrequency>`. `WordFrequency` the the class that Kumo uses to represent a `(word, frequency)` pair. [You can view it's documentation here](http://www.cs.brandeis.edu/~rcmarcus/cs12b/kumo/wordcloud/WordFrequency.html). Finally, once you've got a `List<WordFrequency>`, you should use the [`WordCloud` class](http://www.cs.brandeis.edu/~rcmarcus/cs12b/kumo/wordcloud/WordCloud.html) to generate a word cloud and save it to the appropiate place. The documentation is pretty sparse, so you might find the project's [GitHub page](https://github.com/kennycason/kumo) to be useful.

Here's an example of how to generate a word cloud like the one above:

```
WordCloud wordCloud = new WordCloud(400, 400, CollisionMode.RECTANGLE);
wordCloud.setPadding(0);
wordCloud.setBackground(new RectangleBackground(400, 400));
wordCloud.setFontScalar(new LinearFontScalar(14, 40));
wordCloud.build(wf);
wordCloud.writeToFile("test.png");
```

Here, `wf` is a `List<WordFrequency>`. And that's it! Make sure to use good design patterns and make object-oriented decisions.



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
