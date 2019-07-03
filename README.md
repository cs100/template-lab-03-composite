# Composite Pattern

> Author: Jimmy Tran, Brian Crites ([@brrcrites](https://github.com/brrcrites))

**You must work in a group of two for this lab**

In this lab you will creating a composite pattern for representing an expression tree. An expression tree is a tree representation of an equation, so the expression

```
3 + 7 * 4 - 2
```

would be represented with the following expression tree

![example expression tree](https://github.com/cs100/template-lab-03-composite-strategy/blob/master/images/intro-tree.png?raw=true)

The depth of each operation and its operands is based on their precedence, with the highest precedence operations being at the lowest levels of the tree. The first expression to be executed (in this case, `7 * 4`) will be at the deepest level. The result of that calculation then becomes an input to the addition (which becomes `3 + 28`), and that result becomes an input to the subtraction (`31 - 2`). The tree is evaluated recursively using a head recursive so the evaluation travels from the bottom to the top in order to yield the correct result.

## Setup

To begin this project you will need to add the google unit test framework as a submodule to your repository. You should then create a `main.cpp` and a `unit_test.cpp` the first of which should have a main function that returns 0, the second of which should intialize and execute all google tests. Finally, create a CMakeLists.txt file which is capable of generating two executables (1) a `lab` executable which uses the `main.cpp` file and (2) a `test` executable which uses the `unit_test.cpp`. Verify that this CMakeLists.txt file is capable of generating a makefile and that makefile can build the proper executables.

## Implementation

You will write a composite pattern for representing these expression trees, and are required to use the following abstract base class which has been provided to you in base.h:

```c++
class Base {
    public:
        /* Constructors */
        Base() { };

        /* Pure Virtual Functions */
        virtual double evaluate() = 0;
        virtual string stringify() = 0;
};
```

There are two main functions that you will need to develop for this lab. The first function, `evaluate()`, will return the value of a node while the second, `stringify()`, returns a string representation of the node. Remember that a node in this case could represent a single value or an entire subtree.

> Note: you will need to include the above Base class header file in multiple subclasses, which will cause a duplication error. This error occurs because when you include a header file the contents of that file are essentially pasted to replace that include. This leads to multiple definitions of the Base calss when mulitple includes exist in your program. To solve this you will need to use [header guards](https://www.learncpp.com/cpp-tutorial/header-guards/) which exist to make sure only one copy of a header are included within a program.

You will need to develop the following classes for creating your expression trees:

* class `Op`: This class represents the [operands](https://en.wikipedia.org/wiki/Operand) in an expression, and can be any double value. It's string representation should be a string version of its double value.
* class `Rand`: This class is exactly the same as the `Op` class, but rather than being able to set its value its value is set during construction by the the equation `rand() % 100`
* class `Mult`: This class represents the multiplication operation. It's string representation should be "\*" along with its operands (which might be operands or another operator).
* class `Div`: This class represents the division operation. It's string representation should be "/" along with its operands (which might be operands or another operator).
* class `Add`: This class represents the add operaiton. It's string representation should be "+" along with its operands (which might be operands or another operator).
* class `Sub`: This class represents the subtraction operation. It's string representation should be "-" along with its operands (which might be operands or another operator).
* class `Pow`: This class represents the [power or exponentiation](https://en.wikipedia.org/wiki/Exponentiation) operation. It's string representation should be "\*\*" along with its operands (which might be operands or another operator).

You should notice that any parentheses that would normally be necessary in the expression can be implemented in the tree's structure rather than requiring a specific node or other element to represent it. It is recommend that you utilize the `to_string()` function introducted in c++11 to make the `stringify()` function. You may add parenthesies to your `stringify()` function to make it easier to read, but this is optional. You must create each operation separately and use google test unit tests to fully validate it is functioning before moving on. In this case, this means minimally testing each classes `stringify` and `evaluate` functions to make sure they are returning the correct values. Each operation along with its tests should be it’s own commit (or number of commits) to your GitHub repo. Optionally, you can use the [GitHub flow](https://guides.github.com/introduction/flow/) workflow and create each one as a branch and merge it in once it has been completed. This is an excellent choice if you and your partner are working on different sections at the same time.

## Unit Testing

As stated above you will need to create unit tests for all the classes in this lab, and this will require the development of mocks. While the `Op` and `Rand` classes which hold the base information in this system are fairly small and straightforward, when you test other classes like `Add` and `Pow` using the these classes you are creating a situtaiton in which you cannot know for sure if a test is failing because of the `Add` class or the dependent `Op` or `Rand` class. What would be better is if we could create mock of the object our class under test depends on, in this case the `Op` and `Rand` classes, that simulated the functionality but gave us known, standard, fool-proof (as much as possible with programming) results. For instance, we could create a mock like this.

```c++
class SevenOpMock: public Base {
    public:
        SevenOpMock() { };

        virtual double evaluate() { return 7.5; }
        virtual string stringify() { return "7.5"; }
}
```

Now we have a mock class that will give us a known result which we can use for testing our other classes that utilize leaft classes in their `evaluate()` and `stringify()` functions. It is best to create a few different mocks, since we want to cover a variety of cases which we cannot do with a single mock (at least not in this case). This gives us isolation since now only the class under test or the mock can be causing the failure. The trade off for this isolation is that developing our tests is now relatively expensive, especially if we are testing a complex system with lots of dependencies. It is common to rigirously test the basic units of your design and then use those directly in your testing of other components. This reduces isolation and may make debugging more difficult but saves development time which is a finite resource. There are some places where mocks are essential such as when there are external system that need to be accesssed but do not exist in the testing environment or systems that have transitory or random data such as the `Rand` class.

For this lab, create several mocks for replacing the `Op` and `Rand` class for use in your unit tests.

## Submission

To receive credit for this lab you must show an example program to your TA that demonstrates the full functionality of this pattern, including any interactions with other patterns. You must also show your TA the tests that you have created for validating that your classes are functioning correctly.

