# 3. Explicit State

>"If declarative programming is like a crystal, immutable and practically eternal, then stateful programming is organic: it grows and evolves as we watch."   
>\- Inspired by On Growth and Form, D'Arcy Wentworth Thompson (1860-1948)

- Explicit state is an extension to declarative programming: in addition to depending on its arguments, the component's result also depends on an internal parameter (state).
    - State gives the component a long-term memory, a sense of history.
- Stateless and stateful programming are often called declarative and imperative programming, even though the latter terms are not as precise (but tradition has kept their use).
- Declarative programming has three crucial advandatges over imperative programming:
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

---

<div align="center"><b>
  <a href="2-Declarative-Programming-Techniques.html" style="font-size:64px; text-decoration:none"> < </a>
  <a href="Contents.html" style="font-size:64px; text-decoration:none"> ^ </a>
  <a href="" style="font-size:64px; text-decoration:none">  </a>
</b></div>
