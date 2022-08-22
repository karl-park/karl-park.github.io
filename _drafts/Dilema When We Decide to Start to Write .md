Dilema When We Decide to Start to Write a Test Code

As a mobile developer, I can say we all want to have a high-test coverage.

If there's no test code, I guess the existing codebase might not be good for unit test.

So, we need to refactor the structure to make it testable.
But there's dilema start from here.

We want to ensure that the refactored code is safe, and it should work as properly as the previous code works.
For that, we need to write a test code.

> It's a chicken-and-egg problem.

So.... then, how can we start?

`UI Instrumentation Test`
We can use Instrumentation Test in this case.
Because, we don't need a well-made testable code for the test.
We can easily write down the Instrumentation test just like below.

```

```
