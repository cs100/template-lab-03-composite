# Composite Pattern

> Author: Jimmy Tran, Brian Crites ([@brrcrites](https://github.com/brrcrites))

**You must work in a group of two for this lab**

In this lab you will creating a composite pattern for representing an expression tree. An expression tree is a tree representation of an equation, so the expression

```
3 + 7 * 4 - 2
```

would be represented with the following expression tree

![example expression tree](https://github.com/cs100/template-lab-03-composite-strategy/blob/master/images/intro-tree.png?raw=true)

The depth of each operation and its operands is based on their precedence, with the highest precedence operations being at the lowest levels of the tree. The first expression to be executed (in this case, `7 * 4`) will be at the deepest level. The result of that calculation then becomes an input to the addition (which becomes `3 + 28`), and that result becomes an input to the subtraction (`31 - 2`). The tree is evaluated recursively so the evaluation travels from the bottom to the top in order to yield the correct result.

## Initial Setup

For this lab you will be practicing test driven development to create each of the classes necessary for building your composite pattern before putting it together and testing the entire system. You have been provided with a `unit_test.cpp` file which contains the test runner code for executing google unit tests, an `op_test.hpp` file which has a single basic unit test for the `Op` class, and an `op.hpp` file which contains an incomplete implementation of the `Op` class. You have also been provided with a `base.hpp` file which contains the abstract base class which `Op` inherits from (described in the next section) and finally a `main.cpp` file which you will use at the end of the lab.

Start by creating a `CMakeLists.txt` file which is capable of generating a `test` executable which uses the `unit_test.cpp` file for its main. You should be able to run `cmake3 .` (or simply `cmake .` if not on hammer) from the root directory where the `CMakeLists.txt` file is located and generate the `test` executable. When you run this executable you should see the google unit test output saying that 1 test has executed and it has failed. Do not move on to the next section until you have this portion working.

## Testing Mocks

In this lab you will be creating several classes whose execution is dependent on other classes. As we discussed during the lecture, these types of dependences can make systems hard to test. To make testing easier, especially since you and your partner will be developing in tandem, you should create several mock classes that can be used for testing purposes. Below is a mock for a basic `Op` class which returns a static value.

```c++
class SevenOpMock: public Base {
    public:
        SevenOpMock() { }

        virtual double evaluate() { return 7.5; }
        virtual string stringify() { return "7.5"; }
}
```

Create several of these mocks for different values. Make sure that the values you choose are varied enough to be effective for testing (for example, you should create mocks for negative values and zero as these could be potential edge cases for your system). Using these all the classes can at least be minimally tested until dependent classes can be completed.

## Implementation

You and your partner will develop a composite pattern for representing expression trees, and will need to use the following abstract base class which has been provided to you in `base.h`:

```c++
class Base {
    public:
        /* Constructors */
        Base() { }
        virtual ~Base() {}
        
        /* Pure Virtual Functions */
        virtual double evaluate() = 0;
        virtual string stringify() = 0;
};
```

There are two main functions that you will need to develop for this lab. The first function, `evaluate()`, will return the value of a node while the second, `stringify()`, returns a string representation of the node. Remember that a node in this case could represent a single value or an entire subtree.

> Note: you will need to include the above Base class header file in multiple subclasses, which would normaly cause a duplication error. This error occurs because when you include a header file the contents of that file are essentially pasted to replace that include. This leads to multiple definitions of the Base calss when mulitple includes exist in your program. To solve this we have added [header guards](https://www.learncpp.com/cpp-tutorial/header-guards/) which exist to make sure only one copy of a header are included within a program. Depending on how you develop your classes you may need to include header guards on other files as well, and its standard practice to include them on all header files by default.

You will need to develop the following classes for creating your expression trees:

* class `Op`: This class represents the [operands](https://en.wikipedia.org/wiki/Operand) in an expression, and can be any double value. It's string representation should be a string version of its double value.
* class `Rand`: This class is exactly the same as the `Op` class, but rather than being able to set its value its value is set during construction by the the equation `rand() % 100`
* class `Mult`: This class represents the multiplication operation. It's string representation should be "\*" along with its operands (which might be operands or another operator).
* class `Div`: This class represents the division operation. It's string representation should be "/" along with its operands (which might be operands or another operator).
* class `Add`: This class represents the add operaiton. It's string representation should be "+" along with its operands (which might be operands or another operator).
* class `Sub`: This class represents the subtraction operation. It's string representation should be "-" along with its operands (which might be operands or another operator).
* class `Pow`: This class represents the [power or exponentiation](https://en.wikipedia.org/wiki/Exponentiation) operation. It's string representation should be "\*\*" along with its operands (which might be operands or another operator).

> Note: To avoid ambiguities in the string representation, your `stringify()` function should add parenthesis around all expressions. Thus, for example, "((3+4)**((2+3)-(4/2)))" would be one suitable string representation, though variation is permitted.  You may use the `to_string()` function introducted in c++11 to make the `stringify()` function.

You must create each operation separately and use google test unit tests to fully validate it is functioning before moving on. Make sure to fully test all the functions (`evaluate()` and `stringify()` along with any helper functions) with a range of possible values (both valid and invalid) and using a range of input types where appropriate (for example, use a `Mult` as one of the children when testing an `Add` class and vice-versa). You and your partner should use the [GitHub flow](https://guides.github.com/introduction/flow/) workflow to work on different classes simultantely and then use pull requests (with reviews) to merge code into the `master` branch. For this lab it is recommended that you make one commit per class declaration, function definition, and added unit test.

## "Production" Executable

So far you have developed the classes necessary to build the composite structure and have written tests to validate that they are working properly. Most development work in industry is like this, developing a new classes and functions or modifying existing ones so they work seemelessly in a larger existing system. For this lab integrating your classes into the "production" system means validating that it works with the `main.cpp` file that we provided for you. Add any necessary headers to the `main.cpp` file so that it will compile correctly, then modify your `CMakeListst.txt` file so it can create a new executable `lab` which uses the `main.cpp` file for its main. Once you have done that build and execute the `lab` executable and validate that everything works as exepcted (Note: you will need to run `cmake3 .` (or simply `cmake .` if not on hammer) after modifying your `CMakeLists.txt` file before running `make` to generate the new executable).

## Submission

To receive credit for this lab you must show your TA that you are able to build and run the `lab` executable and generate the correct result and that you have a suite of test cases with appropriate coverage.

