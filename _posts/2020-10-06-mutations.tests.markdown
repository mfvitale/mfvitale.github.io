---
layout: post
title:  "Are you testing your tests?"
date:   2020-10-06 16:42:38 +0200
categories: blog
---
How many of you heard at least one time in their career this sentence: "**We must have a code coverage >= 80% for release to production**"?

<p align="center">
<img src="https://memegenerator.net/img/instances/54739072.jpg" alt="Code coverage is coming" width="50%"/>
</p>

:raised_hand::raised_hand::raised_hand:

As for all absolute measurement you should pay attention and treat it carefully. That's why I totally agree with the following sentence

<blockquote class="twitter-tweet"><p lang="en" dir="ltr">Code coverage is a side effect, not a goal. The only time I find the metric useful is when I’m working with legacy and want to have an indication if there are tests for the part of the code I’m interested in.</p>&mdash; Sandro Mancuso (@sandromancuso) <a href="https://twitter.com/sandromancuso/status/1091701221390516224?ref_src=twsrc%5Etfw">February 2, 2019</a></blockquote>

I think that sometime (lot of times) this "number" is used only as a goal but it should be a "tool" for developers to understand the fragility of the code that they are going to change. 

* High coverage :relaxed:
* Low coverage :fearful:

So how I can say that my code has a good quality? Certainly doing tests and so having a good coverage but..this is not sufficient. Who tests the quality of your test?!

This is where *mutation tests* come in handy.

## What is mutation testing?
<p align="center">
<img src="https://media.nature.com/lw800/magazine-assets/d41586-019-03536-x/d41586-019-03536-x_17373716.jpg" alt="Mutation" width="50%"/>
</p>

Mutation tests are new type of software testing with the aim to test the quality of your test! They works changing some code and see if there are some tests that fails.
I see in this type of tests an analogy with [chaos enginering](https://principlesofchaos.org/) 

> Chaos engineering is the discipline of experimenting on a software system in production in order to build confidence in the system's capability to withstand turbulent and unexpected conditions.

### Basic concept
Mutation is a change automatically seeded into your code that can be **killed** if your tests fails, or **lived** if your tests pass!
The quality of your tests can be gauged from the percentage of mutations killed.

Very simple!

### Coverage vs Mutation
For example the following code is an implementation to check if a string is palindrome.

``` java
public class Palindrome {

    public boolean isPalindrome(String inputString) {
        if (inputString.length() == 0) {
            return true;
        } else {
            char firstChar = inputString.charAt(0);
            char lastChar = inputString.charAt(inputString.length() - 1);
            String mid = inputString.substring(1, inputString.length() - 1);
            return (firstChar == lastChar) && isPalindrome(mid);
        }
    }
}
```
and this is the tests that

``` java
public class PalindromeTest {

    @Test
    @DisplayName("Passing noon to isPalindrome must return true")
    public void when_pal() {
        Palindrome fb = new Palindrome();
        assertTrue(fb.isPalindrome("noon"));
    }
}
```

has a code coverage of **100%** but mutation coverage is instead of **57%**.
![coverage]({{site.baseurl}}/assets/img/mutation-tests/coverage.png)
 in details 
 ![PIT report]({{site.baseurl}}/assets/img/mutation-tests/pit-report.png)

Let's analyze the result starting from the *green* lines: 7, 10, 11 and 12 so *KILLED* mutations:

**7\.** in case we change the *return true* to *return false* when the *lenght* is 0, our test obviously fails so mutation is catched and killed!

**10 and 11.** in that case adding 1 instead to substract it will cause an *ArreyOutOfBoundException* and our test will fails. Even in that case we killed the mutation.

**12.2** in that case the first condition of the *return* statement is negated and our test will fail. Mutation killed!

let's now analyze the red lines: 6, 12 so *SURVIVED* mutations:

**6\.** in case we negate the condition on *lenght*, returning true when lenght is not 0, our test will pass so the change *SURVIVED*. Not good!

> *Solution* add a test with and empty string and expect isPalindrome return true.

**12.1** in that case if the *return* statement in the else will always return true, our test will pass an so the change *SURVIVED*.

**12.3** in that case we are nagatin the '&&' condition of the *return* statement, so we are considering only the first and the last char to say if a string is palindrome or not. Our test will pass and so the change *SURVIVED* 

> *Solution* for 12.* add a test to verify also when a string (with lenght > 2) is not palindrome.

``` java
@Test
@DisplayName("Passing empty string to isPalindrome must return true")
public void when_empty_pal() {
    Palindrome fb = new Palindrome();
    assertTrue(fb.isPalindrome(""));
}

@Test
@DisplayName("Passing mario string to isPalindrome must return false")
public void when_not_pal() {
    Palindrome fb = new Palindrome();
    assertFalse(fb.isPalindrome("mario"));
}

@Test
@DisplayName("Passing neon string to isPalindrome must return false")
public void whenNearPalindrom_thanReject(){
    Palindrome palindromeTester = new Palindrome();
    assertFalse(palindromeTester.isPalindrome("neon"));
}
```
Note that the last test on "neon" is required to also kill mutation on the second part of the return condition. In that case we will have **100%** mutation coverage.

![PIT report 100%]({{site.baseurl}}/assets/img/mutation-tests/pit-report-good.png)

### PIT, Java mutation tests library
Thre are different libraries to enable mutation testing but I think that for the Java world the best is [PIT](https://pitest.org/). It' very simple to use:

First of all add
``` pom
<plugin>
    <groupId>org.pitest</groupId>
    <artifactId>pitest-maven</artifactId>
    <version>LATEST</version>
 </plugin>
```

to your pom.xml and, if you are using Junit5, you need to add these dependency to plugin
``` pom
<dependencies>
    <dependency>
        <groupId>org.pitest</groupId>
        <artifactId>pitest-junit5-plugin</artifactId>
        <version>0.12</version>
    </dependency>
</dependencies>
```
remember to add also surfire plugin to execute test with maven
``` pom
<plugin>
    <groupId>org.apache.maven.plugins</groupId>
    <artifactId>maven-surefire-plugin</artifactId>
    <version>3.0.0-M5</version>
</plugin>
```
Now, you can run:
``` bash
mvn test
```
``` bash
mvn pitest:mutationCoverage 
```
after the execution finish you can find the report in 'target/pit-reports'


You can find the project example [here](https://github.com/mfvitale/mutation-tests-example)

### Conclusion
What we have seen is that code coverage alone is not a good indicator of quality, we need also something to test the quality of out tests. If you put together high code coverage and mutation coverage you can have a great measure of your code quality!
