# An Introduction to Functional Programming
Functional programming has taken the world by storm in recent years. You might see all sorts of pieces of it in the form of callbacks, promises, pattern matching and stateless API (If you don't recognize any of those terms, don't worry). Despite this, it is a paradigm that has been around for as long as computer science has been a topic, and is certainly older than other modes of programming you may be used to. To understand it's role within the context of computing, let's take a look at its history.

### A Little Bit of Math

Computing started as a result of the formalization of procedural techniques in mathematics. Typically, Mathematicians are interested in proving mathematical statements. These mathematical statements are called __propositions__ which are simply statements relating to some mathematical concept that are either true or false. An example of a proposition might be the following: 
* `there are infinitely many prime numbers` 

Over the years, mathematicians have found many techniques for providing evidence for a proposition. These techniques, called __proofs__, are a means of creating new mathematical knowledge. In their most basic form, Proofs offer us evidence for a proposition in such a way that the conclusion must logically follow from the steps of the proof, each step of course could be another proposition or some logical structure of propositions.

Here is an example proof for the proposition above, using a technique called _proof by contradiction_:

*  _Proof_. We will prove by contradiction that there are infintely many primes. 
1. Assume that there are finitely many primes. 
2.  Let C be the product of every prime.
3.  Let P = C + 1
4.  P is not divisible by any number smaller than the largest prime that composes C, because if you divide it by any prime that composes C, you would get a remainder of 1.
  
5. Therefore, The existence of P implies that a prime that is larger than the largest prime that composes C exists. This contradicts the fact that we have multiplied every prime number to arrive at C. 
6. There cannot be finitely many primes, and there are certainly not zero, so there must be an infinite number of primes. _QED_

Even though we have proven that the proposition holds, this proof gives us no way for actually finding such prime numbers.  

Primes are very useful numbers in math. Because of this, we might want to know a method for calculating prime numbers.

What we want is a good step by step process for creating new primes. From here on out, a method, process, procedure or algorithm all refer to the same nebulous concept of a good step by step process.

One way to think about the difference between proofs and algorithms is thinking in terms of knowledge. Proofs are called __Declarative Knowledge__, since a proof declares that some particular fact is true. Algorithms are considered __Imperative Knowledge__ or Procedural Knowledge, since it outlines a sequence of instructions for completing a specific task.

The concept of a procedure is something that mathematicians had struggled with up until the mid 1930s. They coined the term "effective computability" to refer to a way of describing such tasks formally.

### "Effective Computability"

Before computers were machines, computers were humans. A computer had the job of performing a series of tedious calculations by hand. This is why some of the early computers were known as "electronic computers", since "computers" were just humans. But as electronic computers become much faster and more reliable than human ones, we started refering to them simply as "computers". In fact, computing had existed as a profession up until the late 1960s. 

A computer follows a series of instructions, or a procedure, to "compute" a solution to a mathematical problem. These instructions would usually be written as statements in plain language. `"if x then y"` and `"find the greatest common denominator of 35 and 49"` would be examples of such plain statements. But language can be ambiguous. The statement `"Find the multiplicative inverse of X"` might have multiple interpretations. Furthermore, the job of a computer is tedious and not very insightful, therefore creating a machine that could perform general computations would be nice, except that we can't just use plain language to tell it what to do. In order to tell the machine what we want it to do, we will have to __program__ it.

Without having an "effective" way of representing mathematical procedures, it is also hard to reason about them. You might have multiple ways of doing the same thing, but without a formal method, you might not be able to tell which one is more efficient, or if they take the same amount of time to complete (as is the case with many sorting algorithms).

Before the intuition behind "effective computabilty" was formalized, the 20th century mathematician David Hilbert posed the following problem:
* ` Is there an algorithm which given a particular proposition, decides if it is true or false?`

This is called the entscheidungsproblem (or decision problem in english). Such an algorithm would simply put mathematicians out of business. If such a procedure existed, then we could replace mathematicians with computers, and just prove all of mathematics by iterating over all possible propositions. However, before we could even describe such a procedure, we need to have a good way of representing procedures in general. 

And thus the race was on to formalize the intuition of "effective computability". Even though such an algorithm is impossible (Hence why we still have mathematicians to this day, see Gödel's Incompleteness Theorem), the search for such an algorithm had prompted the development of the first formal methods in computing.

From the ashes of the incompleteness of mathematics arose two major methods for formalizing the intuition of "effective computability". 

The first that was discovered uses mathematical functions as a basis for computing. This was Alonzo Church's "lambda calculus", which is considered the first programming language, and is certainly the first functional programming language. I will talk more extensively about the lambda calculus in the next chapter, and use it as an opportunity to introduce some functional programming concepts. 

The second method that was discovered, was the generalization of the concept of a "state machine". This version of computation suggested by Alan Turing, showed that computations were reducible to manipulating the state of some memory device. Turing used an "infinitely long tape" strewn with arbitrary symbols along with a state machine that governed the motion of a head that could read and write data to and from this tape as an example of his general purpose computer. This is now referred to as a "turing machine" in his honor.

Turing soon after proved that both the lambda calculus and the turing machine were capable of expressing the same types of computations, that is to say that anything that can be represented on a turing machine, can be represented in the lambda calculus and vice a versa. This result is the Church-Turing Thesis, and is at the heart of computing. You might consider more mainstream languages such as C and Java as __imperative languages__, where programs are composed entirely of __statements__ that modify the __state__ of the program. These are similar to the state machines of the Turing model of computing. However, the Church-Turing thesis shows that anything you could do in an imperative, state based language, can also be done in a __declarative language__, where programs evaluate __expressions__ that equate describing _what_ a program should do instead of _how_ it should be done. Functional languages are examples of declaritive code, where the emphasis is on evaluating expressions and computing results rather than manipulating variables. This allows functional programmers to worry less about implementation details, and stay focused on the high level organization of code.

Keep in mind that declarative/imperative languages and declarative/imperative knowledge refer to two completely separate concepts. Both declarative and imperative languages can be used to express imperative knowledge. However, one of the benefits of declarative programming is that you could more easily relate the behaivor of declarative programs back to the more declarative styles of proofs in mathematics. This makes it possible to _prove_ propositions about programs that we might be interested in, such as the correctness and guaranteed termination of such a program. 

You might hear that functional programming is a style of programming. But to say that this is a style of programming would be like saying that object oriented programming is also a style of programming. "Style" implies choice, however in a language like Java, everything is an object. Likewise, Haskell is considered a "purely functional" programming language, and thus the only "style" of programming that matters there is functional. The "style" way of talking only makes sense in context of multi-paradigm languages such as Python and JavaScript. Even so, considering it to be a "style" strips it of most of this important context. More accurately, functional programming is a paradigm of computing, where a paradigm is a methodology for achieving a goal by applying specific techniques.

With these two models of computing now fully realized, it was apparent that computing was no longer just mathematics. Mathematics emphasizes proving propositions, whereas computing is the study of "methods", which we refer to interchangeably as algorithms, procedures and functions.