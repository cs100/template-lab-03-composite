# Composite Pattern

> Author: Jimmy Tran, Brian Crites ([@brrcrites](https://github.com/brrcrites))

**You must work in a group of two for this lab**

In the first part of this lab you will creating a composite pattern for representing an expression tree. An expression tree is a tree representation of an equation, so the expression

```
3 + 7 * 4 - 2
```

would be represented with the following expression tree

![example expression tree](https://github.com/cs100/template-lab-03-composite-strategy/blob/master/images/intro-tree.png?raw=true)

The depth of each operation and its operands is based on their precedence, with the highest precedence operations being at the lowest levels of the tree. The first expression to be executed (in this case, `7 * 4`) will be at the deepest level. The result of that calculation then becomes an input to the addition (which becomes `3 + 28`), and that result becomes an input to the subtraction (`31 - 2`). The tree is evaluated recursively from the bottom to the top in order to yield the correct result.

You will write a composite pattern for representing these expression trees, and are required to use the following abstract base class which has been provided to you in base.h:

```
class Base {
    public:
        /* Constructors */
        Base() { };

        /* Pure Virtual Functions */
        virtual double evaluate() = 0;
};
```

Note that the primary function in the base class is `evaluate()`, which will be used to return the value of the node (which may be a subtree).

You will have one type of leaf node which will represent a numberic operand (class `Op`), and two types of composite nodes. There will be four sub-types of nodes that have two children, capable of expressing the operations multiply (class `Mult`), divide (class `Div`), add (class `Add`), and subtract (class `Sub`). There will also be one type of node that has a single child, which expresses squaring a value (class `Sqr`). Notice that any parentheses that would be in the expression can be implemented in the tree's structure rather than requiring a specific node to represent it. You will create each operation separately, creating tests for them using gtest before moving on. Each operation should be it’s own commit (or number of commits) to your github repo. Optionally you can create each one as a branch and merge it in once it has been completed.

You are not required to implement functionality for parsing an expression, but can build the trees by instantiating nodes individually and adding them as children. You should fully test your system by creating expression trees of different sizes, compositions, and complexity.

# Strategy Pattern

In the second part of this lab you will create a strategy pattern for sorting the expression trees that you created by their `evaluate()` value. To perform this sorting you will need to create two containers: one that uses a vector to hold your trees (class `VectorContainer`) and one that uses a standard list (class `ListContainer`). Both of these classes should hold the top node pointers of the trees, so the list or vector would be of the type `<Base*>`. You will implement them both as subclasses of the following `Container` abstract base class, which has been provided to you in container.h. You should create each one independently, creating tests for them using gtest before moving on. Each container should be it’s own commit with a proper commit message. Optionally you can create each one as a branch and merge it in once it has been completed.

```
class Container {
    protected:
        Sort* sort_function;

    public:
	    /* Constructors */
        Container() : sort_function(NULL){};
        Container(Sort* function) : sort_function(function) {};

        /* Non Virtual Functions */
        void set_sort_function(Sort* sort_function); // set the type of sorting algorithm

        /* Pure Virtual Functions */
	    // push the top pointer of the tree into container
        virtual void add_element(Base* element) = 0;
        // iterate through trees and output values
        virtual void print() = 0;
        // calls on the previously set sorting-algorithm. Checks if sort_function is not null, throw exception if otherwise
        virtual void sort() = 0;

        /* Essentially the only functions needed to sort */
        //switch tree locations
        virtual void swap(int i, int j) = 0;
        // get top ptr of tree at index i
        virtual Base* at(int i) = 0;
        // return container size
        virtual int size() = 0;
};
```

Notice that our Container abstract base class does not have any actual STL containers because it leaves the impelementation details of the container to the subclasses.

You **must use the homogeneous interface above for your sort functions, and you are only allowed to manipulate the containers through this interface, not directly**. This will allow you to extend and change the underlying functionality without having to change anything that interfaces with it.

You will also implement two sort functions for sorting these containers, one that uses the selection sort algorithm and one that uses the bubble sort algorithm (you may directly adapt this code when writing your sort functions). They should both be implemented as subclasses of the `Sort` base class below. You should create each one independently, creating tests for them using gtest before moving on. Each sort class should be it's own commit it with it’s own proper commit message. When creating tests for these sort classes, make sure you test them with all the containers you developed previously, and with a number of different expression trees.

```
class Sort {
	public:
        /* Constructors */
		Sort();

        /* Pure Virtual Functions */
		virtual void sort(Container* container) = 0;
};
```

Notice that your container class requires a reference to your sorting class (and vice-versa). This will require you to use [forward declarations](http://www.umich.edu/~eecs381/handouts/IncompleteDeclarations.pdf) and compile your classes as object files (which you should already be doing with CMake). You should test all the combinations of containers and sorting objects together. The following code serves as a basic test you should use to extend your own test cases.

```
#include <iostream>
// #include necessary classes

using namespace std;

int main() {
    Op* op7 = new Op(7);
    Op* op4 = new Op(4);
    Op* op3 = new Op(3);
    Op* op2 = new Op(2);
    Mult* A = new Mult(op7, op4);
    Add* B = new Add(op3, A);
    Sub* C = new Sub(B, op2);
    Sqr* D = new Sqr(C);

    VectorContainer* container = new VectorContainer();
    container->add_element(A);
    container->add_element(B);
    container->add_element(C);
    container->add_element(D);

    cout << "Container Before Sort: " << endl;
    container->print();

    cout << "Container After Sort: " << endl;
    container->set_sort_function(new SelectionSort());
    container->sort();
    container->print();
};
```
