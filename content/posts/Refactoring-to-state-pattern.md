---
title: "Refactoring to State Pattern"
date: 2022-08-09T22:03:46+05:30
author: ["Siddhant Shaw"]
summary: Refactoring is a process of changing a software in a way that doesn't alter the external behaviour of code and yet improve its internal structure. In this post I am trying to explain how one of the bad code smell can be treated using State Patterns.
draft: false
ShowShareButtons: true
ShowToc: true
editPost:
    URL: "https://github.com/Mianto/Mianto.github.io/tree/editpost/content"
    Text: "Suggest Changes" # edit text
    appendFilePath: true # to append file path to Edit link
---
Refactoring is a process of changing a software in a way that doesn't alter the external behaviour of code and yet improve its internal structure. Refactoring is just like performance optimization as both involve carrying out code manipulations that don’t change the overall functionality of the program. The difference is the purpose: Refactoring is always done to make the code _easier to understand and cheaper to modify_.

![Refactoring Process](../../../images/post/refactoring/refactoring_process.png)


Refactoring is generally done when there is tangible and observable indication that there is deeper problem in the system, these are called code smells. There is lot of code smell which includes *Long Method*, *Large Class*, which generally indicates method or class is doing more than single thing.

In this post, I am going to talk about one of the code smell **Repeated Switches**, and how it can be related to **State design pattern**. Repeat Switches occurs where the same conditional switching logic (either in a switch/case statement or in a cascade of if/else statements) pops up in different places. The problem with such duplicate switches is that, whenever a clause is added or needs to be updated, one have to find all the switches and update them.

## State Pattern 
State pattern allows an object to alter its behaviour when its internal state changes. The object will appear to change its class. State pattern is closely related to _Finite state machines_.

![Finite State Machine](../../../images/post/refactoring/state_machine.png)

The main idea is that, at any given moment, there’s a finite number of states which a program can be in. Within any unique state, the program behaves differently, and the program can be switched from one state to another instantaneously. However, depending on a current state, the program may or may not switch to certain other states. These switching rules, called transitions, are also finite and predetermined.

### Implementing State Pattern

Implementing a finite state machine involves series of steps:
* Gather up all the states
* Define the states using a constants or enums and create an instance to hold the current state.
* Gather up all the action that can happen in a state machine 
* Create a class that acts as the state machine. For each action, we create a method that uses conditional statements to determine what behavior is appropriate in each state.

### Example

Lets check how these steps are followed in case of simple gate in metro or subway
* Gate will have three state: `Open`,`Close` and `Process Payment`.
* Defining the states using constants and a variable to hold the current state.
```java
    private final int OPEN_STATE = 0;
    private final int CLOSE_STATE = 1;
    private final int PROCESSING_STATE = 2;
    private int CURRENT_STATE;
```
* Checking up all actions that can happen on the gates, `Enter`, `PayOK`, `PayFail` and, `PayInitiated`.
---
#### States and Action
| States             | Enter       | PayOK | PayFail    |PayInitiated   | 
| ------------------ |:-----------:|:-------:|:--------:|:-------------:|
| Open State         | Close       | Open  | Open       |   Open        |  
| Close State        | Close       | Close | Close      |   Processing  |
| Processing State   | Processing  | Open  | Close      |   Processing  |
---
* For each of the action, create a conditinal to determine the next state of the gate.
```java
    public void enter() {
        if (CURRENT_STATE == OPEN_STATE) {
            System.out.println("Individual has enter the gate, closing gate");
            CURRENT_STATE = CLOSE_STATE;
        } else if (CURRENT_STATE == CLOSE_STATE) {
            System.out.println("Please pay to open the gate");
        } else if (CURRENT_STATE == PROCESSING_STATE) {
            System.out.println("Payment is in process, please wait");
        }
    }
```
Lets test all these behaviours out and be done with our development. Sadly we know that is not true ;). New requirements for state might come in future and we are checking for states at each action at multiple places, which can introduce buggy behaviour in the code and this is a code smell known as **Repeated Switches**.

## Refactoring to State pattern

Before refactoring, we should have a test suite ready which we can use to verify the behaviour. We will make small changes on our way and run the suites to verify the existing behaviours. 

### Steps for refactoring

* **Step-1**: Extract methods from the actions for a given state using `Extract Methods`.
```java

    public void enter() {
        if (CURRENT_STATE == OPEN_STATE) {
            enterForOpenState();
        } else if (CURRENT_STATE == CLOSE_STATE) {
            System.out.println("Please pay to open the gate");
        } else if (CURRENT_STATE == PROCESSING_STATE) {
            System.out.println("Payment is in process, please wait");
        }
    }

    private void enterForOpenState() {
        System.out.println("Individual has enter the gate, closing gate");
        CURRENT_STATE = CLOSE_STATE;
    }

``` 
* **Step-2**: Using `Slide Functions` aggregate all actions of a given state.
```java

    private void enterForOpenState() {
        System.out.println("Individual has enter the gate, closing gate");
        CURRENT_STATE = CLOSE_STATE;
    }

    private void payOkForOpenState() {
        System.out.println(unreachableStep);
        throw new IllegalCallerException(unreachableStep);
    }
    
    private void payFailForOpenState() {
        System.out.println(unreachableStep);
        throw new IllegalCallerException(unreachableStep);
    }

    private void payInitiatedForOpenState() {
        System.out.println("Payment is already processed");
    }

```
* These extracted function becomes the base of the new class representing a state. We need to have an uniform interface to hold different methods to be executed by the a given state containing all actions. 
```java
    public interface GateState {
        void enter();
        void payOK();
        void payFail();
        void payInitiated();
    }
```

* Create a class for the `OpenState` implementing `GateState` and add all the functions to the that state having implementation extracted from the parent class. All our test cases will start to fail. We need a new suite of tests for verifying these changing behaviours. 

* Repeat the same steps for the other GateState: CloseState and ProcessingState.

* Finally Change the `Gate` class having current state only, and the actions delegating their actions to the respective GateStates. 
```java

    public class Gate {
        GateState gateState;

        public void enter() {
            gateState.enter();
        }

        public void payOK() {
            gateState.payOK();
        }

        public void payFail() {
            gateState.payFail();
        }

        public void payInitiated() {
            gateState.payInitiated();
        }
    }
``` 

### Advantage of Refactoring to State Pattern

* Localised the behaviour of a state into it's own class.
* Removed the code smell `Repeated Switch` using polymorphism. 
* Closed the state for modifications and let it remain for extensions, whenever a new requirement for the state arrives lets say to add `Retired State` or `Out of Order State`, code will be open for extension.
* Code has become easier to understand and cheaper to maintain.


## Source Code

Code is hosted at [@github](https://github.com/Mianto/refactoring-to-state-pattern), please reach out for suggestions.


## References
* Refactoring Book (https://martinfowler.com/books/refactoring.html)
* Code Smell (https://martinfowler.com/bliki/CodeSmell.html)
* Code Smell (https://en.wikipedia.org/wiki/Code_smell)
* State Design Pattern (https://refactoring.guru/design-patterns/state)
* Christopher Okhravi, State Design Pattern (https://www.youtube.com/watch?v=N12L5D78MAA)
* Head First Design Pattern (https://www.oreilly.com/library/view/head-first-design/0596007124/)
* Finite State Machine (https://en.wikipedia.org/wiki/Finite-state_machine/)
