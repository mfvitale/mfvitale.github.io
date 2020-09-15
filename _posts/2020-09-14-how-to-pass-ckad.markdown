---
layout: post
title:  "How to pass CKAD"
date:   2020-09-14 18:42:38 +0200
categories: blog
---
In the last years Kubernates is becoming more and more present in everyday (programmers) life, so I think it's important to learn it and why not, to take a certification!

If you are thinking about the CKAD (Certified Kubernates Application Developer) take in mind that this is a pratical certification. As decribed on CNCF site 

> The online, proctored, performance-based test consists of a set of performance-based items (problems) to be solved in a command line and is expected to take approximately two > (2) hours to complete.

I have 10 suggestion for you:

1. **Take an online course** \
    I truly recommend to invest in this course beacuse gives you knwolodges on both theory and practice. There are a lot of excersice to do in an environment very similar to the one used for the CKAD exam. https://www.udemy.com/certified-kubernetes-application-developer/
2. **Pratice a lot** \
    You can also do some practice using [Katacoda](https://www.katacoda.com/). Also here the environment is much similar to the CKAD one.
3. **Pratice a lot** \
    Yes it's not a duplication. Practice is the key to success. In this [repo](https://github.com/dgkanatsios/CKAD-exercises) there are a lot of exercises very similar to the exam ones
4. **No copy&paste** \
    Take in mind that you will not able to copy&paste using usual keys combination (CTRL+C and CTRL+V on windows) so learn other combination. For windows you can use SHIFT+Insert and CTRL+Insert. Avoid also some laptop keyboard that forces you to also press 'Fn' key
5. **Change always context** \
   Since in the exam there are different k8s clusters that may vary question by question I strongly suggest to execute the command to change your context. Always also if not needed. 
6. **Take note of score and skipped question** \
    The scoring pass is 70%. Each question has a weight specified in %. So I suggest to take note in this mode
    1. 6%
    2. 
    3. 7%
    
    So for each question that you think answered correctly put the percentage or leave it blank if you skipped. In this way you can easly see which question you skipped and also you can quickly sum to understood if you reached or not the passing score. During the exam you can access to a note application insede the browser and you can only use it. 

7. **Don't waste time on long questions with low score** \
   There are some question very long to read but with a low score (i.e 1% or 2%). I suggest to skip it if you are taking to long to read it. You can return on it at the end.
8. **VIM is default editor** \
    Practice it or if you hate it as me you can run this command to use **nano** as default editor
    {% highlight bash %}
      export KUBECTL_EDITOR="nano"
    {% endhighlight %}
9. **Define alias for kubectl** \
    The secret is to be fast and you should avoid to waste time typing so create an alias for kubectl
    {% highlight bash %}
    alias kk=kubectl
    {% endhighlight %}
10. **Remember you can have another chance** \
    If all this rule not helped you, remember that you have another chance to re-take the exams for free. 
    
Good luck!
