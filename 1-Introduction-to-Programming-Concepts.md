# 1. Introduction to Programming Concepts

>"There is no royal road to geometry."  
>"Just follow the yellow brick road."  
>\- Euclid's reply to Ptolemy, Euclid (c. 300 BC)  
>\- The Wonderful Wizard of Oz, L. Frank Baum (1856–1919)

- In this chapter, you will get introduced to the most important concepts in programming.
    - Later chapters will give a deeper understanding of these concepts (and add other concepts).

## 1. Calculator
- In Mozart, type: `{Browse 9999*9999}`
- Info:
    - The curly braces are used for a procedure or function call.
    - Browse is a procedure that displays the one argument in the browser window.
- To compile that line, press: `ctrl-. ctrl-l`
- Result: `99980001`

## 2. Variables
- Declare a variable:
```
declare
V = 9999 * 9999
{Browse V*V}
```
- Info:
    - Variables are short-cuts for values, they cannot be assigned more than once. You *can* declare another variable with the same name.
    - The declare statement creates a new **store variable** and makes the **variable identifier** refer to it.
- Result: `9996000599960001`

## 3. Functions
- Factorial: <img src="https://render.githubusercontent.com/render/math?math=\large n! = 1*2*...*(n-1)*n">
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
    - Fact recursive mathematical definition:
        - <img src="https://render.githubusercontent.com/render/math?math=\large 0! = 1">
        - <img src="https://render.githubusercontent.com/render/math?math=\large n! = n*(n-1)! \quad if \quad n>0">
- Call the function *{Fact 10}* inside of the Browse procedure to display the result: `{Browse {Fact 10}}`
- Result: `3628800`
- `{Browse {Fact 10}}`
- Result: `933 26215 44394 41526 81699 23885 62667 00490 71596 82643 81621 46859 29638 95217 59999 32299 15608 94146 39761 56518 28625 36979 20827 22375 82511 85210 91686 40000 00000 00000 00000 00000`

---

<div align="center"><b>
  <a href="Software.html" style="font-size:64px; text-decoration:none"> < </a>
  <a href="Contents.html" style="font-size:64px; text-decoration:none"> ^ </a>
  <a href="2-Declarative-Computation-Model.html" style="font-size:64px; text-decoration:none"> > </a>
</b></div>
