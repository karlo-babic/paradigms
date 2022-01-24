# Wrap Up

> "The removal of much of the accidental complexity of programming
means that the intrinsic complexity of the application is what's left."   
> \- Security Engineering, Ross J. Anderson (2001)

> "If you want people to do something the right way, you must make
the right way the easy way."  
> \- Traditional saying

## Other paradigms
- This section describes two additional computation models (which are related to each other).
- We did not cover all the computation models, and new models will be created in the future.

### Relational programming
- A procedure in declarative model uses its input arguments to calculate the values of its output arguments.
    - For a given set of input argument values, there is only one set of output argument values.
- A relational procedure is more flexible in two ways:
    - there can be any number of results to a call - either zero (no results), one, or more
    - which arguments are inputs and which are outputs can be different for each call
- Relational programming is well-suited for databases, parsers, and for enumerating solutions to complex combinatoric problems.
- Relational programming extends declarative programming with a new kind of statement called "choice".
    - The choice is implemented with search, which enumerates the possible answers.
- Relational programming is practical:
    - when the search space is small
    - as an exploratory tool
- To use search in other cases (when brute force exploration is not possible in real space/time), more sophisticated techniques are needed (e.g., constraint-solving algorithms, optimizations based on the problem structure, and search heuristics).

### Constraint programming
- Constraint programming consists of a set of techniques for solving constraint satisfaction problems.
    - A constraint satisfaction problem (CSP) consists of a set of constraints on a set of variables.
        - "X is less than Y" or "X is a multiple of 3"
- Constraint programming is close to the ideal of declarative programming: to say *what* we want without saying *how* to achive it.
- Propagate-and-search (a general approach for tackling CSPs) is based on three important ideas:
    - Keep partial information -> During the calculation, we might have partial information about a solution (e.g., "in any solution, X is grater than 100").
    - Use local deduction -> Each of the constraints uses the partial information to deduce more information. For example, combining the constraint "X < Y" and the partial information "X > 100", we can deduce that "Y > 101" (assuming Y and X are integers).
    - Do controlled search -> When no more local deductions can be done, then we have to search. The idea is to search as little as possible: we will do just a small search step and then try to do local deduction again.
- Constraint programming is a type of logic programming.

## Conclusion
- There exist many computation models that differ in how expressive they are and how hard it is to reason about programs written in them.
    - The declarative model is one of the simplest, however, it has serious limitations for some applications.
    - There are more expressive models that overcome these limitations, at the price of sometimes making reasoning more complicated.
    - **Rule of least expressiveness**: When programming a component, the right computation model for the component is the least expressive model that results in a natural program.

### Computation models
- **Declarative sequential model** -> This model encompasses strict functional programming and deterministic logic programming. It extends the former with partial values (dataflow variables) and the latter with higher-order procedures.
- **Declarative concurrent model** -> This is the declarative model extended with explicit threads and by-need computation (lazy evaluation). It includes lazy functional programming and deterministic concurrent logic programming.
- **Message-passing concurrent model** -> This is the declarative model extended with communication channels (ports). This removes the limitation of the declarative concurrent model that it cannot implement programs with some nondeterminism.
- **Stateful model** -> This is the declarative model extended with explicit state (mutable variables). This model can express sequential object-oriented programming. In this model components have history.
- **Shared-state concurrent model** -> This is the declarative model extended with both explicit state and threads. This model contains concurrent object-oriented programming. Reasoning with this model is the most complex since there can be multiple histories interacting in unpredictable ways.
- **Relational model** -> This is the declarative model extended with search. It encompasses nondeterministic logic programming. This model is a precursor to constraint programming.

---

<div align="center"><b>
  <a href="4-Declarative-Concurrency.html" style="font-size:64px; text-decoration:none"> < </a>
  <a href="Contents.html" style="font-size:64px; text-decoration:none"> ^ </a>
  <a href="" style="font-size:64px; text-decoration:none">  </a>
</b></div>
