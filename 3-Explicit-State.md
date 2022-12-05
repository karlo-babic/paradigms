# 3. Explicit State

>"If declarative programming is like a crystal, immutable and practically eternal, then stateful programming is organic: it grows and evolves as we watch."   
>\- Inspired by On Growth and Form, D'Arcy Wentworth Thompson (1860-1948)

- Explicit state is an extension to declarative programming: in addition to depending on its arguments, the component's result also depends on an internal parameter (state).
    - State gives the component a long-term memory, a sense of history.
- Stateless and stateful programming are often called declarative and imperative programming, even though the latter terms are not as precise (but tradition has kept their use).
- Declarative programming has three crucial advantages over imperative programming:
    - It is easier to build abstractions in a declarative setting (since declarative operations are compositional).
    - Declarative programs are easier to test (since it is enough to test single calls). Testing stateful programs is harder because it involves testing sequences of calls (due to the internal history).
    - Reasoning with declarative programming is simpler then with imperative programming (algebraic reasoning is possible).
- None the less, imperative programming is a better fit for some types of problems.

## 1. State
- A state is a sequence of values in time that contains the intermediate results of a desired computation.
- There are two basic ways in which state can be present in a program (implicit and explicit).

### Implicit (declarative) state
- Implicit state exists only in the mind of the programmer.
- Example:
```
fun {SumList Xs S}
    case Xs
    of nil then S
    [] X|Xr then {SumList Xr X+S}
    end
end
```
- Each recursive call has two arguments: Xs (the unexamined rest of the input), and S (the sum of the examined part of the input list).
- The call {SumList [1 2 3 4] 0} goes trough a sequence of Xs and S values:
    - [1 2 3 4] # 0
    - [2 3 4] # 1
    - [3 4] # 3
    - [4] # 6
    - nil # 10
- This sequence of Xs#S values is a state. Neither the program nor the computation model "knows" about that state, the state is completely in the mind of the programmer.

### Explicit state
- Explicit state in a procedure is a state whose lifetime extends over more than one procedure call whithout being present in the procedure's arguments.
- Explicit state cannot be expressed in the declarative model. We need to extend the computation model with a kind of container that we call a *cell*.
- Unlike declarative state, explicit state is not just in the mind of the programmer, it is visible in both the program and the computation model.
- We can use a cell to add a long-term memory to SumList. For example let us keep track of how many times it is called:
```
local
    C = {NewCell 0}
in
    fun {SumList Xs S}
        C := @C+1
        case Xs
        of nil then S
        [] X|Xr then {SumList Xr X+S}
        end
    end
    fun {SumCount} @C end
end
```
- Info:
    - Function SumCount is added to make the state observable (to be able to get cell's content outside of the function body).
    - NewCell creates a new cell with initial content 0.
    - @ gets the content.
    - := puts in a new content.
- The ability to have explicit state is very important: it removes the limits of declarative programming.
- With explicit state, abstract data types gain in modularity since it is possible to encapsulate an explicit state inside them.
    - The access to the state is limited according to the operations of the abstract type.
    - This idea is at the heart of object-oriented programming.

#### Exercise 1
- Define a SumList function that uses a cell to sum the numbers in a list, instead of an accumulator.

## 2. State and system building
- *The principle of abstraction* is the most successful system-building principle for intelligent being with finite thinking abilities.
- A system can be built as a series of layers. It is not necessary to understand everything at once.
    - Each layer's implementation interacts with the specification of the lower layer, and provides its own specification for the higher layer.

### System properties
- The three properties a system should have to best support the principle of abstraction:
    - **Encapsulation**: It should be possible to hide the internals of a part.
    - **Compositionality**: It should be possible to combine parts to make a new part.
    - **Instantiation**: It should be possible to create many instances of a part based on a single definition. These instances "plug" themselves into their environment when they are created.

### Component-based programming
- Those three properties (encapsulation, compositionality, and instantation) define component-based programming.
- A component specifies a program fragment with an inside and an outside (i.e., with a defined interface).
    - The inside is hidden from the outside, except for what the interface permits.
- In component-based programming, the natural way to extend a component is by using *composition*: build a new component that contains the original one.
    - The new component offers a new functionality and uses the old component to implement the functionality.

### Object-oriented programming
- Object-oriented programming adds a fourth property to component-based programming:
    - **Inheritance**: It is possible to build the system in incremental fashion, as a small extension or modification of another system.
- Incrementally-built components are called *classes* and their instances are called *objects*.
- Although component composition is less flexible than inheritence, it is much simpler to use.
    - Use inheritence only when composition is insufficient.

## 3. Abstract data types
- An abstract data type (ADT) is a set of values together with a set of operations on those values (as we saw in the previous chapter).
- An ADT with the same functionality can be orginized in many different ways:
    - **Openness**: open or secure.
        - An *open* ADT is one in which the internal representation is completely visible to the whole program.
        - A *secure* ADT is one in which the implementation is concentrated in one part of the program and is inaccessible to the rest of the program.
        - An ADT can be partially secure: the rights to look at its internal representation can be given out in a controlled way.
    - **State**: stateless or stateful.
        - A *stateless* ADT (i.e., a declarative ADT) is written in the declarative model. With this approach, ADT instances cannot be modified, but new ones must be created.
        - A *stateful* ADT internally uses explicit state. Examples of stateful ADTs are components and objects. With this approach, ADT instances can change as a function of time.
    - **Bundling**: unbundled or bundled.
        - An *unbundled* ADT is one that can seperate its data from its operations.
        - A *bundled* ADT is one that keeps together its data and its operations in such a way that they cannot be separated by the user.

### Variations on a stack
- Let us take the \<Stack T\> type and see how to adapt it to some of the eight possibilities:
    - Open, declarative, and unbundled
        - The usual open declarative style, as it exists in Prolog and Scheme.
    - Secure, declarative, and unbundled
        - The declarative style is made secure by using wrappers.
    - Secure, declarative, and bundled
        - Bundling gives an object-oriented flavor to the declarative style.
    - Secure, stateful, and bundled
        - The usual object-oriented style, as it exists in Smalltalk and Java.

#### Open declarative unbundled stack
- The basic stack implementation:
```
fun {NewStack} nil end
fun {Push S E} E|S end
fun {Pop S ?E}
    case S of X|S1 then E=X S1 end
end
fun {IsEmpty S} S==nil end
```
- Example usage:
```
declare S1 S2 S3 X
S1 = {NewStack}
{Browse {IsEmpty S1}}
S2 = {Push S1 23}
S3 = {Pop S2 X}
{Browse X}
```

#### *Secure* declarative unbundled stack
- We make this version secure by using a wrapper/unwrapper pair:
```
local Wrap Unwrap in
    {NewWrapper Wrap Unwrap}
    fun {NewStack} {Wrap nil} end
    fun {Push S E} {Wrap E|{Unwrap S}} end
    fun {Pop S ?E}
        case {Unwrap S} of X|S1 then E=X {Wrap S1} end
    end
    fun {IsEmpty S} {Unwrap S}==nil end
end
```
- The stack is unwrapped when entering the ADT and wrapped when exiting.
    - Outside the ADT, the stack is always wrapped.
    - Once a stack is wrapped (with the *Wrap* function), it can be unwrapped only by the *Unwrap* function that is paired with the *Wrap* function.
- Usage looks the same as in the previous example.
- NewWrapper implementation:
```
proc {NewWrapper ?Wrap ?Unwrap}
    Key = {NewName}
in
    fun {Wrap X}
        fun {$ K} if K==Key then X end end
    end
    fun {Unwrap W}
        {W Key}
    end
end
```

#### Secure declarative *bundled* stack
- Let us now make a bundled version of the declarative stack. The idea is to hide the stack inside the operations, so it cannot be separated from them:
```
local
    fun {StackOps S}
        fun {Push X} {StackOps X|S} end
        fun {Pop ?E}
            case S of X|S1 then E=X {StackOps S1} end
        end
        fun {IsEmpty} S==nil end
    in
        ops(push:Push pop:Pop isEmpty:IsEmpty)
    end
in
    fun {NewStack} {StackOps nil} end
end
```
- This version does not need wrapping to be secure, since wrapping is only needed for unbundled ADTs.
- The function *StackOps* takes a list S and returns a record of procedure values, `ops(push:Push pop:Pop isEmpty:IsEmpty)`, in which S is hidden by lexical scoping.
- Example usage:
```
declare S1 S2 S3 X
S1 = {NewStack}
{Browse {S1.isEmpty}}
S2 = {S1.push 23}
S3 = {S2.pop X}
{Browse X}
```
- Because this version is both bundled and secure, we can consider it as a declarative form of object-oriented programming.

#### Secure *stateful* bundled stack
- Now let us construct a stateful version of the stack:
```
fun {NewStack}
    C = {NewCell nil}
    proc {Push X} C:=X|@C end
    fun {Pop}
        case @C of X|S1 then C:=S1 X end
    end
    fun {IsEmpty} @C==nil end
in
    ops(push:Push pop:Pop isEmpty:IsEmpty)
end
```
- This version provides the basic functionality of object-oriented programming: a group of operations (methods) with a hidden internal state.
- Example usage:
```
S = {NewStack}
{Browse {S.isEmpty}}
{S.push 23}
X = {S.pop}
{Browse X}
```

#### Assignment 1
- Implement the counter component defined below in the four ways that were explained in this section:
    - open declarative unbundled counter,
    - *secure* declarative unbundled counter,
    - secure declarative *bundled* counter,
    - secure *stateful* bundled counter.
- Counter:
    - {NewCounter} should initialize a new counter with value 0.
    - {CountUp} should iterate that value by +1.
    - {CounterVal} should return the current counter value.

## 4. Stateful collections
- Collection is an important kind of ADT: it groups together a set of partial values into one compound entity.
- There are different kinds of collections:
    - Indexed or unindexed collections.
        - Whether or not there is rapid access to individual elements (through an index).
    - Extensible or inextensible collections.
        - Whether the number of elements is variable or fixed.

### Indexed collections
- We have already used (in declarative programming) tuples and records.
    - The *stateful* versions of tuples and records are *arrays* and *dictionaries*.

<p align="center"><img src="https://raw.githubusercontent.com/karlo-babic/paradigms/main/img/indexed_collections.png"><br>Varieties of indexed collections</p>

### Unindexed collections
- Indexed collections are not always the best choice, sometimes it is better to use an unindexed collection.
- Lists are an example of an unindexed collection.

### Extensible collections
- Dictionaries are an example of an extensible collection.
- The extensible array is an array that is resized upon overflow.
    - It has the advantage of constant-time access and significantly less memory usage than dictionaries.

## 5. Case study

### Generating random numbers
- Random number generation can be very useful (statistical sampling, computer simulation, cryptography, and other areas where unpredictability is desirable).

#### Different approaches
- Use unpredictable events in the computer (related to concurrency).
    - Using the thread scheduler as a source of randomness will give some fluctuations, but they do not have a useful probability distribution and they are linked with the computation.
- Rely on a source of true randomness (quantum).
    - For example, electronic circuits generate noise, which is a completely unpredictable signal whose approximate probability distribution is known.
    - Two problems with electronic circuit noise:
        - The probability distribution is not exactly known (it might vary from one circuit to the next or with the ambient temperature).
        - The randomness cannot be reproduced (except by storing the random numbers and replaying them)
- Pseudorandom numbers.
    - We woud like true randomness and we would like it to be reproducible (impossible to have both).
    - Pseudorandom numbers are calculated in a way that appears to generate random numbers, but the generation is reproducible.

#### Uniformly distributed pseudorandom numbers
- A random number generator stores an internal state, with which it calculates the next random number and the next internal state.
- The generator is initialized with a number called its *seed*.
    - Initializing it again with the same seed should give the same sequence of random numbers.
- Definition of the abstract data type of a random number generator:
    - {NewRand ?Rand ?Init ?Max}, which returnes:
        - *Rand* - a random number generator,
        - *Init* - its initialization procedure,
        - *Max* - its maximum value.
    - {Init Seed} initializes the generator with integer seed *Seed*, that should be in the range 0, 1, ..., Max.
    - X={Rand} generates a new random number X and updates the internal state. X is an integer in the range 0, 1, ..., Max-1 and has a uniform distribution (i.e, all integers have the same probability of appearing).
- In our implementation of the generator we will use the linear congruential generator algorithm. If x is the internal state and s is the seed, then the internal state updates as follows:
    - x<sub>0</sub> = s
    - x<sub>n</sub> = (ax<sub>n-1</sub> + b) mod m
- The constants a, b, and m have to be carefully chosen so that the sequence x<sub>0</sub>, x<sub>1</sub>, x<sub>2</sub>, ..., has good properties.
- Stateful implementation:
```
local
    A = 333667
    B = 213453321
    M = 1000000000
in
    proc {NewRand ?Rand ?Init ?Max}
        X = {NewCell 0}
    in
        proc {Init Seed} X:=Seed end
        fun {Rand} X := (A*@X+B) mod M in @X end
        Max = M
    end
end
```
- Usage:
```
declare Rand Init Max
{NewRand Rand Init Max}
{Init 10}
{Browse {Rand}}
{Browse {Rand}}
```
- Lazy implementation (using laziness instead of state):
```
local
    A = 333667
    B = 213453321
    M = 1000000000
in
    fun lazy {RandList S0}
        S1 = (A*S0+B) mod M
    in
        S1|{RandList S1}
    end
end
```
- Usage:
```
declare
L = {RandList 10}
{Browse L.1}
{Browse L.2.1}
```

#### Assignment 2
- Define the function {Uniform} that will generate a uniform distribution from 0 to 1 (use the stateful {Rand} defined above, and the function {IntToFloat X}).
- Define the function {UniformRange A B} that will generate a uniform distribution from A to B (use {Uniform} you defined, and the function {IntToFloat X}).

#### Assignment 3
- Implement a random number generator that uses [nondeterminism in concurrency](https://karlo-babic.github.io/paradigms/1-Introduction-to-Programming-Concepts.html#nondeterminism-and-time) as the source of randomness. Make use of the fact that the time a thread takes to store a value into a cell is nondeterministic (a recursion can happen thousands of times while a value is being stored into a cell).
    - Limit the value to the range between 0 and 1.
- What are the main problems with your implementation? What are the core problems with using concurrency as the source of randomness?
 
 ## 6. Object-Oriented Programming

- *Object-oriented programming* is a useful way of structuring stateful programs.
    - It introduces one new concept, *inheritance*, which allows to define ADTs (abstract data types) in incremental fashion.
- We loosely define object-oriented programming as programming with encapsulation, explicit state, and inheritence.
- Stateful abstract data types are a very useful concept for organizing a program.
    - A program can be built in a hierachical structure as ADTs that depend on other ADTs (component-based programming).
    - Object-oriented programming takes this idea one step further. It is based on the observation that components frequently have much in common.
    - An ADT can be defined as an existing ADT (it inherits from an existing ADT) with some changes.
- An object is an entity that encapsulates a state so that it can only be accessed in a controled way.
- A class is an entity that specifies an object in an incremental way, by defining the classes (its direct ancestors) that the object inherits from and defining how the class is different from the ancestors.
- Inheritance is a *programming technique*, and the underlying computation model (paradigm) of object-oriented programming is simply the stateful model (from the previous chapter: declarative model + explicit state).
    - Object-oriented languages provide linguistic support for inheritance by adding classes as a linguistic abstraction.

---

<div align="center"><b>
  <a href="2-Declarative-Programming-Techniques.html" style="font-size:64px; text-decoration:none"> < </a>
  <a href="Contents.html" style="font-size:64px; text-decoration:none"> ^ </a>
  <a href="4-Declarative-Concurrency.html" style="font-size:64px; text-decoration:none"> > </a>
</b></div>
