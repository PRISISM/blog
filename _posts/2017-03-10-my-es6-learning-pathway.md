---
layout: post
title: My ES6 Learning Pathway
---
ES2015 (ES6) is the latest version of JavaScript and it features a plethora of useful additions to the language. However, not all browsers support it at the moment, although the numbers are rising. Thankfully, we have **transpilers** to make it available anywhere! 

Because of how useful the features seemed, I looked for a practical pathway to learning ES6. In the end, this was the route I took to learning and applying these new skills.

## Learning
To learn the information, I used [Egghead's ES6 course](https://egghead.io/courses/learn-es6-ecmascript-2015), which breaks the new features into small videos with practical examples. I found the tiny chunks to be a lot more useful than some of the other long winded courses I briefly viewed online. 

So after viewing one of these videos, what did I do next?

## Applying
To apply the new features, my first stop was TDD - or Test Driven Development - with [ES6 Katas](http://es6katas.org/). This site features several test suites which are grouped by function. As you can guess, after watching a video, you can then apply these skills by fixing failing tests! 

## Taking it Further
If the tests on ES6 Katas weren't enough, what else can you do to use these features? 

My approach was to first try applying these in exercises on [Codewars](https://www.codewars.com). It has a built in Node + Babel setting for writing ES6 code, which makes it a bit easier to start coding, as opposed to setting up the Babel CLI for yourself. 

The second and fairly obvious approach is to refactor old code to use the new features of ES6! Try changing old, messy concatenation strings to template strings, or make use of the `let` and `const` declarations! This approach will also teach you how to incorporate Babel (or Traceur) into your workflow and task runners. 

## Conclusion
Hopefully this helped anyone looking for a more practical way to learn ES6. If you think there are any better resources out there, feel free to message me or make a pull request with the updated information! 