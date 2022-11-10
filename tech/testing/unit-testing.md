---
title: On Unit Testing
description: Some thoughts & notes on the topic of Unit Testing
date: 2022-04-10

tags:
  - testing
  - software engineering

---
*This post is a stub, to be potentially expanded upon in the future*

It is a long-running joke in the developer community that developers hate writing tests, and I can attest to that from my experience and observations as well. Despite all the collective wisdom about how we should all be writing unit tests and how these tests help with future enhancements and regression, many projects end up not having enough tests, or tests that are there mainly to chalk up sufficient code coverage to satisfy the client.

## Why should we write tests in the first place?

> "Testing is the activity of implementing a program or system with the sole aim of finding errors" (Myers, 1979)

Contrary to what some developers may think, testing is not done to see if something works, or worse, to get enough code coverage to keep the client or management happy — it is to test *if it doesn't work*.

Rather than treating code coverage as a chore, we should think of code coverage as an indication of whether our codes are aligned to business use cases. Code coverage tells you whether your test cases reach those lines of codes. If there is no test case that reaches those lines, then how do we know the scenario is even required in the first place?

The value of unit tests is most appreciated when we have to deal with regression scenarios later, e.g. when modifying an existing feature. The number of broken test cases arising from a small change may also help us to assess the change impact (although in reality it is probably not so simple).


## What is a unit test?

> A unit is the smallest piece of code in your program that is repeatable, testable, and functional. Units can be functions, classes, methods, and so on.

Unit tests focus on testing the units comprising the system. In theory, since they are smallest components in the system, they should be easy to test and quick to run.

The "testing pyramid" recommends that unit tests form the majority of tests.

## What makes a good unit test?

I came across this acronym FIRST which helps as a mnemonic:

- **Fast** - Fast to write, fast to execute.
- **Independent** - Does not depend on outcome of previous tests. Tests should be able to run in any sequence.
- **Repeatable** - Does not depend on external factors. Given the same inputs, the same outcome is always obtained. Hence, the only a test can fail (once correct), is that there is a bug in the implementation.
- **Self-Validating** - Don't require humans to read logs, etc, to decide whether the test passed.
- **Timely** - Write tests together with the codes as they evolve over time. Don't introduce them just before going into production.

## Unit Testing Best Practices

This is a list of unit testing best practices I collected so far:

- **Write Readable Tests**
  - **Arrange, Act, Assert** - all tests should follow this pattern.
  - **Consistent naming convention** - what unit is tested, what scenario, expected result
  - It should be *quick and easy* to know *what the test does*.
    - Too often the test codes are overwhelmed by setup codes (i.e. the "Arrange" section), which are also copy-paste-edited to other similar test scenarios - these should be refactored out.
    - It's worse when there are loops and conditional logic
    - Keep *implementation details out of the test*.
    - Write out the desired operation and assert first, this should inform what functions you should implement. In a sense what we are doing is to create a "business language" to describe your test.
- **Avoid Magic Numbers**
  - i.e. numbers without any explanation what they are
- You should not be testing *private methods* directly
- The test should not require change when you change the implementation of the interface?
  - Should only need to change if the interface itself changed (this include the returned result)
- **Tests are Deterministic**
  - Ensure tests do not depend on other test cases (a common fault, and many test runners prefer to determine the test order automatically)
  - If you want tests to be coupled together, they should be grouped together
- **Reduce test dependencies**
- **Assert only one thing**. One use case per unit test
- **Parameterised tests**
  - Don't just copy, paste and edit. If you are doing that, it generally means something...
- **Write tests during development**, not after...
- **Automate the tests**
- **Maintain the tests**
  - If a test is broken (due to changes in requirements), *fix it*!
- **Keep proper test documentation**

## What might be the difficulties faced by developers?

Here are some possible reasons why a developer might not write unit tests. I might discuss them further in the future.

- Not understanding what makes a component *testable* in the first place.
- Not sure which *layer of abstraction* should the unit test address. Frontend and backend testing have different approaches as well.
- Writing tests that depends on previous tests - tests therefore run slower.
- Not understanding that *unit tests are also code*, with all the best practices about being readable, commented, maintainable, etc. Like codes, unit tests may also need re-factoring.
- Not having *enough time* for writing tests, which could be due to personal or external reasons.
- No one to advise on *organising the tests* - running the entire suite may take too long.


## Miscellaneous stuff

### Document Driven Design?

Recently I came across an article describing what the author termed as "Document Driven Development" (or "Design Driven Development", as compared to "Test Driven" and "Behaviour Driven"). I haven't come across this term before, and after googling around it turns out this is a term that has been floating around for years. I'm not sure what might be the cause that led to the coining of this term, but to me all professional application development are based on documented requirements, and it is foolish to write tests or codes before doing any initial design or documentation. Even for TDD which advocates writing tests first before any development, it doesn't say to start without any design in mind. Documenting your design is the best way to review it and ensure that it is correct, and that it addresses the requirements. Don't keep the design in your mind, especially if you are working in a team. And don't tell me your unit tests (and codes) are a living documentation of the requirements - definitely not at the unit test level. To me, that "Document Driven Development" even exists, is a symptom of people not understanding testing...

