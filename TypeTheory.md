# Type Theory & Type Systems

## What is a Type Theory?

A Type Theory usually refers to a system of restricting the behavior of mathematical objects by assigning it a type. That seems very abstract because it has roots in mathematics, logic and philosophy. We can use Type Theory to impose particular rules on the behavior of our programs, which can help us reason about them, and help us write programs that in turn behave more predictably. Type Theory has many variants and is still an active area of mathematical research. 

## Motivations for Type Theories

As we saw in the previous chapter, the Lambda Calculus lets us encode statements like `(λx.x x)(λx.x x)`, which never normalized. Even though this pattern of self application allowed us to write recursive programs and thus very powerful programs, It also showed that the Lambda Calculus is a dangerous tool. It is capable of expressing any computation that could be feasably performed by a general computer (this condition is called "turing completeness", and is related to the work on the entsheidungsproblem by Alan Turing and Alonzo Church). 

The version of the entsheidungsproblem as it applies to computing is called the halting problem: is there a general algorithm, that given a particular program and some particular input, could decide whether or not the program will terminate? I will provide a version of the proof of the fact that no such algorithm in the extra notes of this chapter. Even though the general case is not solveable, there are certain programs that we can reason about using a type theory. 

In general, type theories are more restrictive than other logical systems. The first set theory, proposed by the mathematician Gottlob Frege, was not very restrictive on the application of the `∈` (membership) operation. Thus it allowed you to encode, as Bertrand Russell pointed out, the set of all sets that do not contain themselves: `S ::= { x | x ∉ x}`. Thus the statement `S ∈ S` becomes undecideable (If we say it's true, then `S` can contain itself, which contradicts it's definition. If we say that it is false, then we say that `S` does not contain itself, but it should since that is the definition of `S`). This is basically a liar's paradox situation, except encoded with Naïve Set Theory.

Russell's Paradox lead to two different approaches to creating a foundation for mathematics. The most successful attempt to axiomatize mathematics, is the Zermelo-Frankel Set theory, which is an enhancement of Frege's original set theory. The other approach was the development of the first Type Theory, developed by Russell and Whitehead.

## Elementary Type Theory

A Type is a collection of mathematical objects, to which every object may only belong to once. So for instance, the Number `4` might have a type of Natural Number, or `Nat`. However since 4 is a member of `Nat`, it cannot be a member of any other type. This is a big place where type theory differs from set theory. The members of sets are not restricted to any particular set.

The way that we would say the above statement is in the form of a typing judgement: `4 : Nat`. This roughly translates to the statement "4 has the type Natural Number". Statements where we declare that something exists in some type is called a "typing judgement" or just "judgement" for short. In normal mathematics, we deal with propositions, that we combine to build up proofs. However our typing judgements don't really correspond to propositions, since a proposition can be either true or false. A judgement __*must*__ hold. So the statements
`4 : Nat` and `4 : Reals` are incompatable with each other. You must choose to accept one type to be a member. Another way of saying `4 : Nat` is that "`4` is a value of type `Nat`". `4` is a value, and the instantiation of `4` is the same as producing a value of type `Nat`.

There is a clear distinction between values and types. a value can have a type, thus we would write: `Value : Type`. (In the programming language `Haskell`, the typing judgement is represented with a double colon `::` operator, also called a "Paamayim Nekudatayim", instead of a single colon, since a single colon represents the `cons` operator). If our values could belong to multiple Types, then it wouldn't be obvious which type a value belonged to at a particular time. We can reason about types using logic and correspondences between propositions and types. Even though our judgements must be true, we will see that types can prove propositions. So lets take a look at some basic types, to get a feel for them:

#### The Unit Type

This is the simplest type one could imagine. It is a type that has only one value occupying it. Whereas there are an infinite number of natural numbers, there is only one unit object. often we will refer to this unit type as an empty Tuple: `() : ()` which is the same as `unit :: Unit`. Also, we will use an accepted notation for talking about types, which is that values are lowercased, and types are uppercased.

#### The Boolean Type

This is a fairly familiar type for computer programmers. the `Boolean` type (denoted `Bool`) has two possible values: `true` and `false`. You can imagine the `Unit` type as Boolean, except that `false` was not a memeber of it. Typically, any type that only has one "value constructor" or one way of decribing values, is considered a unit type.
 
#### The Empty Type

The empty or `Void` type is a type that has no values in it. There are no value constructors of the Void type. This doesn't seem like a useful thing to have, but we will soon see how we can use this idea to help us develop a logical system.

#### Product Types

The Product Type is actually a way of combining Types. the product of two types is a type that requires two values of each respective type, in order to construct. Products are often represented as a tuple, or as a cartesian product (×). Here's an example instantiation:
```
4 : Nat
unit : Unit

(4, unit) : Nat × Unit
```
the combination of the Nat type and Unit, is the creation of a type that expects both a natural and the unit value in order to construct.

#### Sum Types

A sum type is also a combination of types, except it represents a choice between two types. so a type that is the Sum of `Nat` and `Bool` is a type whose value is either a Natural number, or a Boolean. However it would be problematic if just said for instance `4 : Nat + Bool` since I said that `4 : Nat`. In order to make this clear, we need to define two other functions on the Sum type. the left function takes the Sum type, and returns the `left` type, and the other takes the `right` type in the sum. Instead we would write it as:
```
4 : first (Nat + Bool)
true : second (Nat + Bool)
```
since `left (Nat + Bool)` evaluates to `Nat`, and `right (Nat + Bool)` evaluates to `Bool`.

when specifying things that are sum types, we might need to use these functions on types, which are called dependent types in more rigorous texts.

#### Functions

A function defines a type that translates between two types. So the type of a function from `Nat` to `Bool`, (for instance, the isPrime function) can be represented as so:
```
f : Nat -> Bool
```
We see that the function `f` expects a value of type `Nat`, and will return a value of type `Bool`.

#### Type variables

Sometimes we don't care about the actual membership of some type. instead we want to refer to it abstractly. This is where we use something called a "type variable". These are usually uppercase letters, since types are themselves usually uppercase. For instance, a function `g` from type `A` to type `B` can be represented as follows:
```
g : A -> B
```
We will see that this allows us to give more specific definitions of the `left` and `right` functions:
```
left : A + B -> A
right : A + B -> B
```

## The Logic of Types

The founding principle of Type Theory is that every value that we can create belongs to one type, and one type only. We can use Type Theory to then impose certain constraints on our logic. For instance, we will want to ensure our logic is consistent above all else. logical consistency means we cannot prove false statements from any true ones. Imagine if you could prove that a false statement was true, such as 1 = 0. it would let you get away with all sorts of dangerous things, such as the result that 0 = 1 = 2 = 3 = ... and so on. We want to guarantee that false statements cannot be proven. So in our logic, we will say that falsehood corresponds to the `Void` type. There are no ways to construct a value of the `Void` type, so we could never prove something false. Thus, in order to prove anything, we need be able to construct a value that corresponds to the proposition we are trying to prove. On the converse, something is not true if there are no values that can be constructed of the type of that proposition.

thus, values correspond to evidence for some type. If a type has a value occupying it, then that value is direct evidence for the truth of the corresponding proposition. This idea is called the "curry-howard isomorphism", or "Propositions as types"

There are other natural conversions between logic and types. For instance, the Product type corresponds to a logical AND in propositional logic. Here is an example of some logically tautologies involving AND and their corresponding Type Representations:
```
(A AND B) IMPLIES A
```
This means "If I know `A AND B` is true, then I know `A` is true":
```
f :  A × B -> A
```
It's one thing to be able declare the type of an arbitrary object, namely `f`, but if we can find a particular value that satisfies the type of `f`, that would prove that that `f` cannot be false, since falsehood corresponds to the `Void` or uninstantiable type.
Here is a possible implementation of `f`:
```
f (a, b) = a
```
`f` takes a pair of `a : A` and `b : B` and returns a value of type `A`. This function would be a concrete instantiation, or a value that satisfies the type `A × B -> A`. Since a value of this type exists, then it is possible to prove that the logical statement `(A AND B) IMPLIES A`. Let's look at some other examples. `(A OR B) IMPLIES A` is an untrue statement, however the converse: `A IMPLIES (A OR B)`, holds.
Could we instantiate a value of the type: `g : A + B -> A`. such a function does not have the complete information of A in the case we have a `right B` type. Howe are we supposed to produce a value of type `A` if our function is given a `B`.
```
f (left a) = a
f (right b) = ?
```
Our function when given a `right B` cannot return an `a`. Remember that `right B` and `left A` are the two types of our sum `A + B`. What about the converse? It's actually a fairly obvious mapping:
```
f a = left a
```
we pull the left type from the sum. This function corresponds to saying that "If I know A , then I know A or B". So our functions correspond to an implication in logic.

```

```

## The Algebra of Types





## Proof of the Halting Problem