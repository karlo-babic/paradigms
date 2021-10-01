# 1. Introduction to Programming Concepts

>"There is no royal road to geometry."  
>"Just follow the yellow brick road."  
>\- Euclid's reply to Ptolemy, Euclid (c. 300 BC)  
>\- The Wonderful Wizard of Oz, L. Frank Baum (1856–1919)

- In this chapter, you will get introduced to the most important concepts in programming.
    - Later chapters will give a deeper understanding of these concepts (and add other concepts).

## 1. Calculator
- In Mozart, type: `{Browse 9999*9999}`
    - To compile that line, press: `ctrl-. ctrl-l`
- Result: `99980001`
- Info:
    - The curly braces are used for a procedure or function call.
    - Browse is a procedure that displays the one argument in the browser window.

## 2. Variables
- Declare a variable (press `ctrl-. ctrl-b` to compile the whole buffer): test
```oz
declare
V = 9999 * 9999
{Browse V*V}
```
- Info:
    - Variables are short-cuts for values, they cannot be assigned more than once. You *can* declare another variable with the same name.
    - The declare statement creates a new **store variable** and makes the **variable identifier** refer to it.
- Result: `9996000599960001`

## Excercise
1. Calculate 2^100 without typing 2\*2\*2... with one hundred twos.
    <details>
        <summary>Hint</summary>
        Use variables to store intermediate results.
    </detalis>
2. Calculate 100! without typing 1\*2\*3... until 100. Can it be done?
    <details>
        <summary>Hint</summary>
        It can't be done (that simply).
    </detalis>

## 3. Functions

### Factorial
- Factorial definition: <img src="https://render.githubusercontent.com/render/math?math=\large n! = 1*2*...*(n-1)*n">
- Factorial of 10: `{Browse 1*2*3*4*5*6*7*8*9*10}`
- Result: `3628800`
- Define a function:
```
declare
fun {Fact N}
    if N==0 then
        1
    else
        N*{Fact N-1}
    end
end
```
- Info:
    - The function body contains the **if expression** instruction.
    - **Recursion**: function is calling itself.
    - Fact mathematical recursive definition:
        - <img src="https://render.githubusercontent.com/render/math?math=\large 0! = 1">
        - <img src="https://render.githubusercontent.com/render/math?math=\large n! = n*(n-1)! \quad if \quad n>0">
- Call the function *{Fact 10}* inside of the Browse procedure to display the result: `{Browse {Fact 10}}`
- Result: `3628800`
- `{Browse {Fact 100}}`
- Result: `933 26215 44394 41526 81699 23885 62667 00490 71596 82643 81621 46859 29638 95217 59999 32299 15608 94146 39761 56518 28625 36979 20827 22375 82511 85210 91686 40000 00000 00000 00000 00000`

### Combinations
- Combination definition: <img src="https://render.githubusercontent.com/render/math?math=\large \binom{n}{r} = \frac{n!}{r!(n-r)!}">
- Combination defined in Oz:
```
declare
fun {Comb N R}
    {Fact N} div ({Fact R}*{Fact N-R})
end
```
- Info:
    - **Functional abstraction**: using existing functions to define a new function.
- `{Comb 10 3}`
- Result: `120`

## 4. Lists
- List is a sequence of elements: `{Browse [5 6 7 8]}`
- *[5 6 7 8]* is a short-cut.
    - A list is a chain of links, and each link contains two things: a list element and a reference to the rest of the chain.
    - *[6 7]* is linked as follows: *6 -> 7 -> nil*, where *nil* is the  empty list.
        - In Oz: `Z=nil  Y=7|Z  X=6|Y`
        - Now *X* references the list *[6 7]*.
- *H|T* is a list pair (often called a [cons](https://en.wikipedia.org/wiki/Cons))
- Example:
```
declare
H = 5
T = [6 7 8]
{Browse H|T}
```
- Result: `[5 6 7 8]`
- Info:
    - H|T = 5 | [6 7 8]
    - Head: *5*
    - Tail: *[5 6 7 8]*
- Get back the head and tail:
```
declare
L = [5 6 7 8]
{Browse L.1}
{Browse L.2}
```
- Result:
```
5
[6 7 8]
```

### Pattern matching
```
declare
L = [5 6 7 8]
case L of H|T then
    {Browse H} {Browse T}
end
```
- Result is the same as the last: *5* and *[6 7 8]*.
- Info:
    - Case instruction declares two local variables: *H* and *T*.
    - Case decomposes L according to the pattern *H|T*.

## 5. Functions over lists
- Pascal's triangle:
```
   1
  1 1
 1 2 1
1 3 3 1
```
- We will define a function, *{Pascal N}*, which calculates the nth row of Pascal's triangle.
- Calculating the fifth row from the fourth:
    - Fourth row: *[1 3 3 1]*
    - Shift the fourth row left and right (by adding a zero to the right and left), and sum them.
        - *[1 3 3 1 0] + [0 1 3 3 1] = [1 4 6 4 1]*, which is the fifth row.

### Pascal's triangle in Oz:
```
declare Pascal AddList ShiftLeft ShiftRight

fun {Pascal N}
    if N==1 then [1]
    else
        {AddList {ShiftLeft {Pascal N-1}}
                 {ShiftRight {Pascal N-1}}}
    end
end

fun {ShiftLeft L}
    case L of H|T then
        H|{ShiftLeft T}
    else [0] end
end

fun {ShiftRight L} 0|L end

fun {AddList L1 L2}
    case L1 of H1|T1 then
        case L2 of H2|T2 then
            H1+H2|{AddList T1 T2}
        end
    else nil
    end
end
```
- Info:
    - Top-down software development: first writing the main function and filling in the blanks afterwards.
- Execute: `{Pascal 20}`
- Result: `[1 19 171 969 3876 11628 27132 50388 75582 92378 92378 75582 50388 27132 11628 3876 969 171 19 1]`

## 6. Correctness

---

<div align="center"><b>
  <a href="Software.html" style="font-size:64px; text-decoration:none"> < </a>
  <a href="Contents.html" style="font-size:64px; text-decoration:none"> ^ </a>
  <a href="2-Declarative-Computation-Model.html" style="font-size:64px; text-decoration:none"> > </a>
</b></div>
