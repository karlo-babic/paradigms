# 2. Declarative Programming Techniques

>"The nice thing about declarative programming is that you can write a specification and run it as a program. The nasty thing about declarative programming is that some clear specifications make incredibly bad programs. The hope of declarative programming is that you can move from a specification to a reasonable program without leaving the language."   
>\- The Craft of Prolog, Richard O’Keefe

- An operation is declarative if it always returns the same results (when called with the same arguments) independent of any other computation state.
- A declarative operation is:
    - independent,
    - stateless,
    - deterministic.
- Declarative programming is important because of two properties:
    - Declarative programs are compositional.
        - They consist of components that can each be written, tested, and proved correct *independently* of other components and of their own past history (previous calls).
    - Reasoning about declarative programs is simple.
        - Simple algebraic and logical reasoning can be used.
- Not all programs can be easily written in the declarative model.
    - As many components (in a program) as possible should be declarative.
- This chapter explains how to write practical declarative programs.
- The basic technique for writing declarative programs:
    - Consider the program as a set of recursive function definitions.
    - Use high-orderness to simplify the program structure.

## 1. Iterative computation
- An iterative computation is a loop whose stack size is bounded by a constant (independent of the number of iterations).

### General schema
- Starts with an initial state S<sub>0</sub> and transforms the state in steps until reaching a final state S<sub>final</sub>:
    - S<sub>0</sub> -> S<sub>1</sub> -> ... -> S<sub>final</sub>
- General schema:

<p align="center"><img src="https://raw.githubusercontent.com/karlo-babic/paradigms/main/img/general_schema.png"></p>

- Info:
    - Functions *IsDone* and *Transform* are problem dependent.
    - Stack size does not grow when executing *Iterate*.

### Newton's method
- Newton's method for calculating the square root of a positive real number *x* is an iterative computation.
- It starts with a guess *g* and improves that guess iteratively until it is accurate enough.
- The improved guess *g'* is the average of *g* and *x/g*.
    - *g'* = (*g* + *x/g*) / 2
```
fun {Sqrt X}
    Guess = 1.0
in
    {SqrtIter Guess X}
end

fun {SqrtIter Guess X}
    if {GoodEnough Guess X} then Guess
    else
        {SqrtIter {Improve Guess X} X}
    end
end

fun {Improve Guess X}
    (Guess + X/Guess) / 2.0
end

fun {GoodEnough Guess X}
    {Abs X-Guess*Guess}/X < 0.00001
end

fun {Abs X} if X<0.0 then ~X else X end end
```
- Additional info:
    - [Video about Newton's method, polynomials, and fractals](https://www.youtube.com/watch?v=-RdOwhmqP5s)

#### Assignment 1
- Define a function *Abs* that calculates the absolute value of a *real* number.
- The following definition does not work:
```
declare
fun {Abs X}
    if X<0 then ~X
    else X
    end
end
```
- Why not? Correct it.
    <details>
        <summary>Hint</summary>
        The problem is trivial.
    </detalis>

#### Assignment 2
- Define a *Cbrt* function (cube root) that uses the Newton's method to calculate it.
    - The formula for calculating the improved guess: *g'* = (*2g* + *x/g<sup>2</sup>*) / 3

### Using local procedures
- Several helper functions are defined in the Newton's method program above: *SqrtIter*, *Improve*, *GoodEnough*, and *Abs*.
- Where to define helper functions?
    - A function defined only as an aid to define another function should not be visible elsewhere.
- Dependency:
    - *SqrtIter* is needed in *Sqrt*,
    - *Improve* and *GoodEnough* are needed in *SqrtIter*,
    - *Abs* is a utility function that could be used elsewhere.
- There are two basic ways to express this visibility:
    1. All the helper functions are defined in a local statement outside of Sqrt.
    2. Each helper function is defined inside of the function that needs it.

#### Way 1
- All the helper functions are defined in a local statement outside of Sqrt.
```
local
    fun {Improve Guess X} ... end
    fun {GoodEnough Guess X} ... end
    fun {SqrtIter Guess X}
        if {GoodEnough Guess X} then Guess
        else
           {SqrtIter {Improve Guess X} X}
        end
    end
end
in
    fun {Sqrt X}
        Guess=1.0
    in
        {SqrtIter Guess X}
    end
end
```

#### Way 2
- Each helper function is defined inside of the function that needs it.
```
fun {Sqrt X}
    fun {SqrtIter Guess X}
        fun {Improve Guess X} ... end
        fun {GoodEnough Guess X} ... end
    in
        if {GoodEnough Guess X} then Guess
        else
            {SqrtIter {Improve Guess X} X}
        end
    end
    Guess=1.0
in
    {SqrtIter Guess X}
end
```

#### Way 2, simplified
- Each helper function sees the arguments of its enclosing function as external references.
    - This means we can remove these arguments from the helper functions.
```
fun {Sqrt X}
    fun {SqrtIter Guess}
        fun {Improve} ... end
        fun {GoodEnough} ... end
    in
        if {GoodEnough} then Guess
        else
            {SqrtIter {Improve}}
        end
    end
    Guess=1.0
in
    {SqrtIter Guess}
end
```

#### Final definition
- There is a trade-off between putting the helper definitions outside the function that needs them or putting them inside:
    - Putting them **inside** lets them see the arguments of the main function (therefore they need fewer arguments). But each time the main function is called, new helper functions are created.
    - Putting them **outside** means that the functions are created once, for all calls to the main function. But then the helper functions need more arguments.
- The final definition (below) balances that trade-off (between efficiency and visibility).
    - *SqrtIter* is local to *Sqrt*.
    - *Improve* and *GoodEnough* are outside *SqrtIter*.
```
fun {Sqrt X}
    fun {Improve Guess} ... end
    fun {GoodEnough Guess} ... end
    fun {SqrtIter Guess}
        if {GoodEnough Guess} then Guess
        else
            {SqrtIter {Improve Guess}}
        end
    end
    Guess=1.0
in
    {SqrtIter Guess}
end
```

### Control abstraction
- Here is once again the schema for the *iteration* control flow:

<p align="center"><img src="https://raw.githubusercontent.com/karlo-babic/paradigms/main/img/general_schema.png"></p>

- We will now turn that schema into a program component, making the schema a *control abstraction*.
    - The schema has to be parameterized by extracting the parts that vary from one use to another.
        - *IsDone* and *Transform* are such parts.
```
fun {Iterate S IsDone Transform}
    if {IsDone S} then S
    else S1 in
        S1 = {Transform S}
        {Iterate S1 IsDone Transform}
    end
end
```
- Info:
    - Arguments *IsDone* and *Transform* accept one-argument functions.
- We can make *Iterate* behave like *SqrtIter* by passing it the functions *GoodEnough* and *Improve*:
```
fun {Sqrt X}
    { Iterate
        1.0
        fun {$ G} {Abs X-G*G}/X < 0.00001 end
        fun {$ G} (G+X/G)/2.0 end }
end
```
- This is a powerful way to structure a program because it separates the general control flow from this particular use.

#### Assignment 3
- The half-interval method is a technique for finding roots of a function *f* (the x values such that the function *f(x) = 0*), where *f* is a continuous real function.
- If we are given points *a* and *b* such that *f(a) < 0 < f(b)*, then *f* must have at least one root between *a* and *b*.
- Steps to calculate a root:
    - Let `x = (a+b)/2` and compute *f(x)*.
    - If *f(x) > 0* then *f* must have a root between *a* and *x*, otherwise the root is between *x* and *b*.
    - Repeat until the calculated *x* is accurate enough.
- Write a declarative program to solve this problem using the techniques of iterative computation.
    - {HalfInterRoot F A B}
        - Pass a continuous real function to the argument F.
        - Pass values *a* and *b* to arguments A and B.
        - Return a value that approximates a root of the function.
        <details>
            <summary>Example</summary>
            Roots of the function: f(x) = x<sup>2</sup>-2<br>
            <img src="https://raw.githubusercontent.com/karlo-babic/paradigms/main/img/function_roots.png">
        </detalis>

## 3. Recursive computation
- Iterative computation is a special case of *recursive computation*.
    - While iterative computation calls itself once (and has a constant stack size), recursive computation can call itself more than once.
- Recursion occurs in two major ways: in functions and in data types.
    - A function is recursive if its definition has at least one call to itself.
    - A data type is recursive if it is defined in terms of itself (e.g., list).

<p align="center"><img src="https://raw.githubusercontent.com/karlo-babic/paradigms/main/img/escher_gallery.jpg"><br>Print Gallery (M. C. Escher)</p>

- Iterative computation has a constant stack size, which is not always the case with recursive computation.
    - It is important to avoid growing stack size whenever possible.
- The factorial implementation below is an example of a recursive computation that is not iterative (its stack size is growing).
- Factorial mathematical definition:
    - <img src="https://render.githubusercontent.com/render/math?math=\large 0! = 1">
    - <img src="https://render.githubusercontent.com/render/math?math=\large n! = n*(n-1)! \quad if \quad n>0">
- Implementation:
```
fun {Fact N}
    if N==0 then 1
    elseif N>0 then N*{Fact N-1}
    else raise domainError end
    end
end
```
- Info:
    - This defines the factorial of a big number in terms of the factorial of a smaller number.
    - Since all numbers are nonnegative, they will bottom out at zero.

#### Growing stack size
- In the factorial implementation above, multiplication comes *after* the recursive call (tail recursion).
    - During the recursive call the stack has to keep information about the multiplication for when the recursive call returns.

#### Converting a recursive to an iterative computation
- Previous implementation for factorial calculation:
    - `3! = (3*(2*(1*1)))`
- We can rearrange the numbers like this:
    - `3! = (((1*3)*2)*1)`
- The second calculation can be done incrementally.
- The iterative definition that calculates factorial in this way is:
```
fun {Fact N}
    fun {FactIter N A}
        if N==0 then A
        elseif N>0 then {FactIter N-1 A*N}
        else raise domainError end
        end
    end
in
    {FactIter N 1}
end
```
- Info:
    - The function that does the iteration, *FactIter*, has a second argument *A* without which iterative factorial would not be possible. It effectively serves as memory for the intermediate results of multiplication.

## 4. Programming with recursion
 
---

<div align="center"><b>
  <a href="1-Introduction-to-Programming-Concepts.html" style="font-size:64px; text-decoration:none"> < </a>
  <a href="Contents.html" style="font-size:64px; text-decoration:none"> ^ </a>
  <a href="" style="font-size:64px; text-decoration:none"> > </a>
</b></div>
