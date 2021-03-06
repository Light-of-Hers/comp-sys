---
layout: default
title: Programming Language
nav_order: 30
permalink: docs/prog-lang
has_children: true
---

# Programming Language 
{: .no_toc }

1. TOC
{:toc}

Reference:

- Types and Programming Languages
- Essentials of Programming Languages
- Programming Language Pragmatics

Way to define _syntax_:

- Grammar
- Terms
  - inductively: inference rules = axioms + proper rules
  - concretely

_Semantics_ styles:

- Operational Semantics:
  - terms as states
  - transition between states as simplification
  - _meaning_ of t is the final state starting from the t
- Denotational Semantics:
  - defining an interpretation function mapping terms into elements of a collection of _semantic domains_.
- Axiomatic Semantics:
  - take the laws (properties) as the definition
  - the meaning of a term is just what can be proved about it

Evaluation: The _one-step evaluation relation_ is the smallest binary relation on terms satisfying the evaluation rules.

- Uniqueness of normal forms (also known as Confluence): If t →∗ u and t →∗ u′, where u and u′ are both normal forms, then u = u′.
- Termination of Evaluation: For every term t there is some normal form t′ such that t →∗ t′.
- Big-step (also known as "natural semantics") vs small-step: 
  - Big-step often leads to simpler proof
  - Big-step cannot describe computations that do not produce a value (Non-terminating or stuck)

Derivation Tree: Induction on Derivation

A term t is in _normal form_ if no evaluation rule applies to it.

- Every value is in normal form.
- If t is in normal form, then t is a value. (Prove by contradiction then by structural induction).
- A closed term (a term that does not contains any free variables) is _stuck_ if it is in normal form but not a value.

|                 | family of | indexed by |
| --------------- | --------- | ---------- |
| Lambda          | Terms     | Terms      |
| System F        | Terms     | Types      |
| Lambda-ω        | Types     | Types      |
| Dependent types | Types     | Terms      |

- Lambda calculus `λx. λxs. cons x xs`
- Dependent types `cons: Π n:Nat. Nat->NatList n->NatList (succ n)`

## Lambda

Russell's paradox => halting problem

Alonzo Church and Alan Turing

Lamdash calculus:

- A formal system invented by Alonzo Church in the 1920s
- A core calculus, capturing the language's essential mechanisms

terms = 

- x     (variable)
- λx. t (abstraction)
- t t   (application)

- alpha renaming
- beta redution (evaluation strategy): λ is confluent.
  - full beta redution 
  - normal order: the lerftmost first
  - call by name: no redutions inside abstraction; the leftmost reduced first
  - call by need: lazy evaluation
  - call by value: reduced only when the right-hand side is a value; the outermost reduced first

Church Boolean:

- tru = λt. λ.f t
- fls = λt. λ.f f
- test = λl. λm. λn. l m n
- and = λb. λc. b c fls
- or = λb. λc. b tru c
- not = λb. b fls tru 

Church Numerals:

- 0 = λs. λz. z
- 1 = λs. λz. s z
- 2 = λs. λz. s (s z)
- succ = λn. λs. λz. s (n s z)
- succ = λn. λs. λz. n s (s z)
- plus = λm. λn. λs. λz. m s (n s z)
- times = λm. λn. m (plus n) 0
- iszro = λm. m (λx. fls) tru
- minus = λm. λn. λz. n pred z = λm. λ.n n pred
- pred = λn. λs. λz. n (λg. λh. h (g s)) (λu. z) (λu. u)

Pairs:

- pair = λf. λs. λb. b f s
- fst = λp. p tru
- snd = λp. p fls

Recursion:

- Terms with no normal form are said to _diverge_.
- omega = (λ x. x x) (λ x. x x)
- Y-Combinator
  - Y = λf. (λx. f (x x)) (λx. f (x x))
  - Y will diverge for any f (assuming call-by-value)
- call-by-value Y-Combinator:
  - fix = λf. (λx. f (λy. x x y)) (λx. f (λy. x x y))
  - fix f = f (λy. (fix f) y)
  - [reinvent-y](https://yinwang0.wordpress.com/2012/04/09/reinvent-y)
  - The Little Schemer Page 160 Chapter 9 

de Bruijin presentation: λx. x (λy. y x) x => λ. 0 (λ. 0 1) 0 => λ. 0 (0 0)

## Types

Safety (Soundness) = Progress + Preservation

Well-typed: `t : T`

- Progress: A-well typed term is not stuck
- Preservation: If a well-typed term takes a step of evaluation, then the result term is also well-typed.

Typing relation: the smallest binary relation between terms and types satisfying all instances of typing rules.

Uniqueness of Types (However, in some languages, a term may have multiple types)

Syntactic sugar; Derived form; Abbreviation for a term.

Some important properties:

- Inversion of the subtype relation: If [relation about S], then S has the form F.
- Substitution
- Cononical Forms: If v is a closed value of type T, then v has the form F.
- Preservation
- Progress
- Erasure: Type annotations play no role in evaluation
- Typability
- Normalization: evaluation of every well-typed program terminates

`fix` itself cannot be defined in simply typed lambda-calculus. Indeed, no expression that can lead to non-termination computations can be typed using only simple type.

## Reference

computational effect / side effect / impure feature

Haskell expresses side effects using _monadic_ action.

In most programming languages, variables are mutable. A variable provides both

- a name that refers to a previously calculated value, and
- the possibility of overwriting this value with another (which will be referred to by the same name)

In some languages (e.g., OCaml), these features are separate. There are three basic operation: 

- allocation `ref`
- dereference `!`
- assignment `:=`

Aliasing sometimes confounds programmers and compilers:

`lambda r: Ref Nat. lambda s: Ref Nat. (r := 2; s := 3; !r)`

A reference names a _location_ in the _store_ (a.k.a _heap_ or _memory_). We can think of the store as a partial function from locations to values!

Store typing: a finite function mapping locations to types.

## Exception

`error` can be any type. It introduces two problems:

- From the point of view of implementation, the language is not _syntax-directed_ any more. (They cannot just be "read from the bottom to top" to yield a typechecking algorithm)
- The Uniqueness of Typing theroem breaks.

Alternative:

- Use variable type for `error`
- Use the minimal type `Bot` for `error` (`Bot` (bottom type) is a subtype of any type)

Exceptions are _value-carrying_ in the sense that one may pass a value to the exception handler when the exception is raised. Exception values have a single type, T_{exn}, which is shared by all exception handler.

## Subtyping

- invariant: S <: T, T <: S => [statment involve S <: T]
  - covariant + contravariant
    - Array
- covariant: S <: T => [statment involve S <: T]
  - "output a value"
    - the function result
    - `Source`
    - the output of a pipe
- contravariant T <: S => [statment involve S <: T]
  - "input a value"
    - the function argument
    - `Sink`
    - the input of a pipe

_minimal typing_: assign each typable term its smallest possible type.

syntax-directed rules: each rule can be "read from bottom to top"

- input position: all metavariables appearing in the premises also appear in the conclusion (not syntax-directed)
- output position: all metavariables appearing in the conclusion also appear in the premises (syntax-directed)

## Type Variable

`S > t`: type t is an instance of type scheme S

## Polymorphism

- subtype polymorphism (OO-style)
- parametric polymorphism (ML-style)
- ad-hoc polymorphism (static / dynamic overloading)

principal types: the most general solution for the constraint typing relation

principal unifier (or the most general unifier)

## OOP and its primitive form

- dynamic dispatch: when an operation is invoked on an object, the ensuring behavior depends on the object itself, rather than being fixed once and for all.
- encapsulation of state
- inheritance: avoid duplication of code
- late binding `this` / open recursion
- super

Object interfaces fit naturally into a *subtype relation*.

Abstract data type (ADT) is similar to OO encapsulation in that only the operations provided by the ADT are allowed to directly manipulate elements of the abstract type.  But different in that there is just one (hidden) representation type and just one implementation of the operations — no dynamic dispatch.

Today's languages is criticized for mixing dynamic dispatch and inheritance features in `class`. Some OO languages offer an alternative mechanism, called **delegation** or **aggregation** (Golang), which allows new objects to be derived by refining the behavior of existing objects.

- structural type system: when recursive types are considered, some of its simplicity and elegance slips away (in C# language)
- nominal type system (Java and most other mainstream languages)

## Recursive Types μ

The primary content of the recusive type metathoey is about the subtyping relation.

Universal Set U: everything in the world

Generation Function:  A function F ∈ P(U) → P(U) is monotone if X ⊆ Y implies F(X) ⊆ F(Y).

- X is F-closed: F(X) ⊆ X
- X is F-consistent if X ⊆ F(X). 
- X is a fixed point of F if F(X) = X.

**Knaster-Tarski Theorem (1955)**:

- The intersection of all F-closed sets is the least fixed point of F. Denoted as μF
- The union of all F-consistent sets is the greatest fixed point of F. Denoted as vF
- Corollary
  - Principle of induction: If X is F-closed, then µF ⊆ X. 
  - Principle of coinduction: If X is F-consistent, then X ⊆ νF.

## Universal Type and System F

ploymorphism:

- Parameteric: system F
- Ad-hoc / overloading

selfApp = λx:any X.X→X. x [any X.X→X] x 
selfApp : (any X. X→X) → (any X. X → X) 

quadruple = λX. double [X→X] (double [X])
quadruple : any X. (X→X) → X → X

System is able to encode Bool / Church / List / Pair !

Property of System F:

- Well-typed System F term are normalizing.
- Theorem: It is **undecidable** whether, given a closed term s in which type applications are marked but the arguments are omitted, there is some well-typed System F term t such that erase_partially(t) = s.
  - Type reconstruction is as hard as **higher-order unification**. (But many practical algorithms have been developed)
- Erasure and Evaluation Order: need to keep the type abstraction:
  - earse\_v(λX. t) = λ\_. erase\_v(t)
- Free Theorem
- Impredicativity: A definition (of a set, a type, etc.) is called “impredicative” if it involves a quantifier whose domain includes the very thing being defined.
  - T = any X. X → X ranges over all types, including T itself

Fragments of System F:

- Rank-1 (prenex): type variables should not be instantiated with polymorphic types
- Rank-2: A type is said to be of rank 2 if no path from its root to a `any` quantifier passes to the left of 2 or more arrows.
- Type reconstruction for ranks 2 and lower is decidable, and that for rank 3 and higher of System F is undecidable.

## Existential Types

used for 

- abstrct data type
- Module

p = {*Nat, {a=5, f=λx:Nat. succ(x)}} as {exist. X, {a:X, f:X→X}}; 

views:

- logical:  an element of {exist. X,T} is a value of type [X → S] T, for some type S.
- operational:  an element of {exist. X,T} is a pair, written as { \*S, t }, of a type S and a term t of type [X → S] T.

Existential Types can be encoded by System F.