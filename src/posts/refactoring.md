---
title: Refactoring
date: '2019-09-06'
---
This week I worked on the Gilded Rose kata, an exercise in refactoring. It's basically a 40+ lines `if` statement with no tests. The goal is to add a new feature while not breaking anything that's currently working. We are given customer requirements and some restrictions on what we can and can't change.

This is my work - https://github.com/agonzalez0515/gilded-rose-refactor

The first thing I tried to do was understand the code, but you quickly realize that this would take a lot of time and brain power. At one point there are 5 nested `if`s! To keep track of each branch to see what it does and how it would break would pretty much lead to something breaking in ways you had no idea it could.

This means I had to write tests! The plan was to add tests to safe guard against changing any current features. Once I had tests in place, if I make a change and something breaks, my tests will give me the immediate feedback.

One of my mentors recommended I take a look at [Characterization Testing](https://michaelfeathers.silvrback.com/characterization-testing) as a way to get started with the tests. These kinds of tests record the behavior of what your program is currently doing, not what it should do or requirements once said it would do. The goal is to preserve behavior. You write a test that will fail, maybe giving the assertion something like an empty string or null value, and when it fails, the error message will tell you what the actual result was. Then you copy that over to the assertion and your test will pass. This feels like cheating because it's kind of reverse TDD, but it's not because this is better than having no tests at all. If the program works as is, then you just need to make sure it keeps working. By having these kinds of tests, you may not find any current bugs, but you're putting in place a mechanism to find bugs later. 

With all that said, I wrote a bunch of characterization tests for the Gilded Rose kata. Using the requirements, I would try to find edge cases by changing the values I would pass to the items and see what the actual result was. I also added a coverage gem, SimpleCov, to the project to give me an idea of when I had a high coverage rate, kind of a guide to when I felt comfortable that I had covered most use cases.

Tests are done, do I add the new feature now? Well no, I'm already here and tests are giving me a safe guard to change, so why not leave it cleaner than I found it? I started refactoring! I looked for small changes and things that were repeated. For example, I saw that this `if` was repeated and I could extract it to a small method responsible only for increasing quality. 
``` ruby
if item.quality < 50
 item.quality = item.quality + 1
end
```

My goal was to flatten the nesting `if`s and take out as much logic out of the one current method, break it down to smaller single responsibility methods with descriptive names. The current code was not explicit at all, it never told you what it did, it just did it. 

## What I Learned/Tips

* Make sure the code has tests and if it doesn't add them
* Take your time adding tests. The tests are _part of the code_, and if you miss a use or edge case, something will break and you won't have the feedback that it did. Don't rush through the tests just to get something done and move on to the "fun" part, the code.
* Use coverage as a guide. It's not the end all of your tests, but it's a handy guide. I had missed an edge case until I added coverage and saw a line was not tested. 
* Read books and watch videos specifically about refactoring. 
* Make small changes, you can always go back and change more.
* Watch for patterns, but don't immediately divert when one emerges. Keep going on your path but make a note of the pattern to revisit later. It's better to finish a thought and keep your tests passing, than switching thoughts and breaking something because you wanted to get ahead of the abstraction.


