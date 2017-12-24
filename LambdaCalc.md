# The Lambda Calculus, a Minimalist Programming Language

## Mathematical Functions

Functions are familar objects to mathematicians, so it is no wonder that the first method of effective computabilty relies on them. A function can be seen as a relation between two sets of things, which maps an element from one set (the domain) to an element of another (the codomain). Sometimes these sets might be the same. In fact, most mathematical functions that you are familiar with are functions from the set of real numbers, to itself. 

When defining a function, sometimes we will write the name of the function, and then some parameters in parenthesis, followed by an equal sign, which denotes the body of the function.

The function: `h(x) = 5x^3 - 3x^2 + 4x - 1` denotes a polynomial function, which is a linear combination of arbitrary degree terms of some variable. 

You might see that when simply calling `h(4)`, we are expressing the result of computing this polynomial with a parameter of `4`. In a sense, mathematical functions have the useful property of hiding the complexity of the computation as the body of the function itself. The computation of course, is as simple as replacing the __placeholder__ `x` with a __value__ of `4` and simplifying the resulting expression as much as possible.

## Lambda Calculus

Alonzo Church made one insight that gave Lambda Calculus the power it has. He realized that functions can serve as mappings on the set of functions itself. This means that a function might be regarded as a parameter of another function, and might return another function as a result:

```Haskell
let t(x) = g where g(y) = x
```
In this example, the function `t` takes a parameter `x` and returns a function `g`, which returns the original parameter to `t`, namely `x`. You can consider `g` to be a constant function, since it ignores it's input, `y`, and returns the same value no matter what it's input is. We will find that functions which input and output other functions, or __higher order functions__, allow us to represent any useful computation we might want.

To make this process more concise, Church used the `λ` to denote the creation of a function:
```Haskell
let i = λx.x
```
`i` is the identity function, it has one parameter, and returns that value.

The lambda denotes the beginning of the parameters of the function `i`. the `.` denotes the beginning of the body of the function. Here's a way to rewrite `t` using λ notation:

```Haskell
let t = λx.(λy.x)
```

Remember that we are allowed to define functions that return other functions:

```Haskell
let f = λx.i
```
Like `g` earlier, The function `f` ignores it's input and returns the identity function. 

Lambda calculus has a few other simple rules:
* Everything in lambda calculus is a function, therefore everything in Lambda Calculus is also a higher order function.
* Since everything is a function and the only thing we can do with functions is apply them to arguments, we can simply denote function application by putting two functions next to each other. The expression ` f t` means "f applied to t" which is `f(t)` in normal mathematics. 
* Our functions can only have one argument, but since we can return others functions, this allows us to write functions to accept multiple arguments.

However, parenthesis in normal mathematics can mean either function application or grouping, and since we will want to do both in lambda calculus, we will omit the role of parenthesis as function application to avoid ambiguity. So instead, we use parenthesis to denote the order in which functions are applied (this is grouping). If we want to evaluate `a b c` as "b applied to c, then a applied to that" we can write the expression as `a (b c)`. 

This also means that function application is naturally left associative. As an example, the expression `a b c d` means "a applied to b then that applied to c then that applied to d" which is also the same as `(((a b) c) d)`.

Lets see some examples of how we might evaluate the functions that we've created earlier. Here are two examples of applying the false function to two other functions.

```
f t f = (λx.i) t f
      = i f
      = f
f f t = (λx.i) f t
      = i t
      = t
```
So the function `f` takes one argument and returns a function that also takes an argument. In a sense, `f` is a function of two arguments, so it can be applied to two parameters and return the latter. Let's look at the function `t`:
```
t t f = (λx.(λy.x)) t f
      = (λy.t) f
      = t
t f t = (λx.(λy.x)) f t
      = (λy.f) t
      = f
```
The function `t` does the exact opposite of `f`, it takes two arguments and returns the former. 

In a sense, these two functions encode a choice between two options. This is the essence of what a boolean does, hence why I called these functions t and f (true and false). Church described encodings between mathematical objects (such as booleans), and functions in the lambda calculus to prove that lambda calculus could represent any computation you might want to express. For instance here are some example implementations of logical operations in the lambda calculus:

```
not = (λa.a f t)
and = (λa.(λb.a b f))
or  = (λa.(λb.a t b))
```
As an exercise in learning how the Lambda Calculus, I would recommend trying to evaluate these functions on the values of true and false as you would in normal logic. Here are some general tips for evaluating expressions in Lambda Calculus:
* Substitute named functions with their λ definitions. To avoid complexity, I would recommend only substituting the left most expression for its λ definition.
* If the left most character is a lambda, then apply it's next argument, until no more functions are left. This process of function application is called beta reduction, and the most simplified version of an expression is called its beta-normal form.
* If you know how another function works quite well, (such as t and f), then it might not be necesary to actually do this lexigraphical substitution. If you are convinced that a particular function does what it supposed to, then just do that simplification as far as you can do so.

Here's an example evaluation of the expression `and (not f) t` :
```
and (not f) t = (λa.(λb.a b f)) (not f) t
              = (λb.(not f) b f) t
              = (not f) t f
              = ((λa.a f t) f) t f
              = (f f t) t f
              = t t f
              = t
```   

From here on out, I will use `f` to denote an arbitrary function, so if you see an `f` in the body of a function with an accompanying `λf.` binding, then know `f` is meant to be a function. if `f` however lacks an accompanying λ term, then it actually signifies the false function as we have defined it. To avoid confusion, from here on out, I will try to use the following substitutions for `t` and `f`:
```
true  = t = λx.(λy.x)
false = f = λx.(λy.y) =  λx.i
```


## Counting with Lambdas

You may have caught on to something odd I said earlier: "Everything in lambda calculus is a function". You might be wondering how we could represent numbers. Like booleans, Church defined an encoding of numbers as well:
```
0 = λf.λx.x
1 = λf.λx.f x
2 = λf.λx.f (f x)
3 = λf.λx.f (f (f x))
4 = λf.λx.f (f (f (f x)))
.
.
```
Imagine this as a function that takes a function `f` and returns another function on `x` that calls `f` on x, some number of times, equal to the number that our function represents. So Zero calls `f` on `x` zero times, One calls `f` on `x` one time, and so on. By representing the numbers as repeated composition, it makes it defining the mathematical operators on numbers convenient. We can start with the increment (or successor) function, which returns the next number:
```
succ = λn.λf. f (n f) 
```
The way this function works might be a bit confusing, so you may want to work through why this works. An even scarier function to define is the decrement or predecessor function. This returns the previous number:
```
pred = λn.λf.λx.n (λg.λh.h (g f)) (λy.x) i
```
I wouldn't recommend trying to make too much sense of it other than to remember that it is the same as `pred(x) = x - 1` in normal mathematics. Since our numbers are simply repeated function composition, we can write arithmetic operations like so:
```
add = λn.λm. (m succ) n
```
addition just takes two numbers, and calls the `succ` function on `n`, `m` many times. Subtraction is very similar:
```
sub = λn.λm. (m pred) n
```
We assume that `n` and `m` are number functions, therefore `n` and `m` expect two parameters (an `f` and an `x`). However, at the point where we call `(m pred)` or `(m succ)`, we haven't given `m` all of the arguments that it needs. After evaluating the expression `(m pred)`, we actually get a function that takes a number, and calls `pred` on it `m` many times. The meaning of this expression should be apparent from the way that we have defined our number functions `n` and `m` as repeated composition. In this case, we are repeatedly composing the successor function `succ(x) = x + 1` to get an `"add one m many times"` function, which we then apply to n to finally get `n + m`.

This technique is sometimes called partial application. Every function in the Lambda Calculus is a function of one argument. Any function that can be applied to multiple arguments must return another function when applied to only one argument. Such "partially applied" functions are also called curried functions, named after the logician Haskell B. Curry for which the Haskell programming language and this technique of currying is named after. 
 
Haskell Curry worked on the field of "combinatory logic", which tried to serve as an alternative model for computation. Ultimately the constructs of combinatory logic, called combinators, were found to have equivalent λ definitions. 
 
Currying can be useful for defining functions without dealing with actual values. programming in this style, that deals with composition and currying instead of specific values is called point-free programming. It is a style that functional programmers like to use, since it seems to be more enlightening that simply a bunch of case examples, to understand the behavior of a function.

To continue our mathematical functions, let's add multiplication to our repertoire:
 ```
mult = λn.λm. m (add n) 0
 ```
Here we have to curry the `add` function with a value of `n`. we use m to apply this new "add by n" function to a starting value of zero.

Lets take a look at the exponential function:
 ```
exp = λn.λm. m (mult n) 1
 ```
This function is very similar to multiplication, except we replace repeated addition with repeated multiplication.

Division is a little harder to encode using church numerals. We will get to it once we have discussed recursion.

## Immutable Data Structures as Functions

Before we talk about recursion, we will build up some useful data structures. But first, we will need to talk about how we treat data in functional programming languages. When we use a functional programming language, the `=` operator is truly the same as equality in math. In other programming languages, the `=` operator defines an "assignment" of some variable. As a result, some techniques which make complete sense in an imperative language make absolutely no sense in functional programming:
```javascript
x = 1
x = 2
```
In an imperative language, you would read the program from the top down. We start with a variable assignment of `x` to a value of `1`. `x` is then "assigned" to a different value of `2`. The final value of the variable `x` is `2` in our imperative language. But in a functional language, where the `=` means true equality, our placeholder `x` can only be one thing ever. This property is called __immutability__, since out values never change, or "mutate". So in a pure functional language, this program makes no sense, because we would be asserting that `1 = 2`. 

However, we gain something from having immutable placeholders. Since we know our expressions can have mathematical equivalence to other expressions, we lose nothing from replacing any expression to any other truly equivalent expression. the ability for you to replace any expression with any other equivalent one is called __referential transparency__. However, this no longer holds true if our functions could affect the state of the program outside of themselves, or depended on values outside of its scope, then these functions might behave the differently in different contexts. Referential transparency is a guarantee that our functions behave the same no matter what context we use them in, provided we give them the same input. In fact, the whole reason we can "simply" or reduce expressions in Lambda Calculus, is because we have a guarantee that the reduced form of an expression is the same as the original form. So we know that our functions behave consistently everytime we use them, and that this would not be the case if we allowed functions to modify state outside of themselves.

Now that we have taken a look at immutibility, we can now discuss the role of data structures in Lambda Calculus. We know that the goal of programs are not to modify the state of some data structure as it would be in an imperative language. Instead we need to use data structures to augment the organization of our program. Like everything in the Lambda Calculus, we can represent data structures as functions. Here's a representation of a pair of two things `a` and `b`:
```
pair = λa.λb.λf.f a b
```
Our `pair` function wraps a function `f` around two expressions `a` and `b`. let's see how we might access values from `a` and `b`:
```
fst = λa.λb.a
snd = λa.λb.b
```
The `fst` function takes two arguments (an `a` and a `b`) and returns the first. The `snd` function returns the latter. Notice that these two functions are also equivalent to our church-encodings of `true` and `false`. Here's an example of how we might use these methods:
```
example = pair 1 2
        = (λa.λb.λf.f a b) 1 2
        = (λb.λf.f 1 b) 2
        = (λf.f 1 2)
```
Now let's evaluate `example fst`:
```
example fst = (λf.f 1 2) fst
            = fst 1 2
            = (λa.λb.a) 1 2
            = (λb.1) 2
            = 1
```
I think you get the idea. Let's see how we might use pairs to create more complex data structures. Let's start with creating a linked list. There are two important parts of a list: the head of the list (first element), and the rest of the list. We will need a value that we can use to tell if we have reached the end of the list, which is in a sense equivalent to the empty list `[]` in other programming languages. This is called the `nil` value:
```
nil = λf.true
```
I set `nil` to a value that returns `true` for convienience. Our `pair` function will call a function on it's arguments. our `nil` function will ignore it's input function and return `true`, as opposed to `pair`, which calls it's input function on `a` and `b`. The way we can distinguish a pair from the end of a list is using the following `isNil` function:
```
isNil = λlist. list (λa.λb.false)
```
If p is a pair, then return `false`, otherwise, return `true` by the definition of `nil`. We will prove that `isNil` does what it's suppose to do:
```
isNil nil = (λp. p (λa.λb.false)) nil
          = nil (λa.λb.false)
          = (λf.true) (λa.λb.false)
          = true

isNil example = (λp. p (λa.λb.false)) example
              = example (λa.λb.false)
              = (λf.f 1 2) (λa.λb.false)
              = (λa.λb.false) 1 2
              = (λb.false) 2
              = false
```
With this, we have everything we need to define lists. So the list `[1,2,3,4]`, would be represented as: 
```
(pair 1 (pair 2 (pair 3 (pair 4 nil))))
```
This is also starting to get very recursive, so instead we might write a function that constructs lists for us: 
```
cons = λval.λlist. pair val list
```
We will use the empty list `[]` as a shorthand for `nil` and the colon `:` for an infix shorthand for the `cons` function. Infix is a way of writing a function as between two values instead of preceeding them (which is called prefix, and how we've written functions in the Lambda Calculus). You have used infix notation before, since you have written mathematical expressions like `3 = 1 + 2` instead of as ` 3 = (+ 1 2)`. Likewise, the list `[1,2,3,4]` can be represented as either `cons 1 (cons 2 (cons 3 (cons 4 nil)))` or as ` 1 : 2 : 3 : 4 : []`.

We only care about two parts of a linked list: the first element (head) and the rest of the list (tail). Since our lists are pairs, we can define them as follows.
```
head = λlist. list fst
tail = λlist. list snd
```

#### A sidenote about LISP

When LISP was the dominant functional programming language, these functions were called the Contents Address Register `CAR`, and Contents Decrement Register `CDR` respectively. This is because registers in LISP machines had two parts, one that pointed to the location of the `head` of the list, and one that pointed to the `tail` of the list. The process of retrieving the head or tail of a list was just retrieving the `address` part of the register or the `decrement` part of it, and loading whatever was in the memory at those locations. So if your memory was 16 bits large, then it made sense to make registers that were 32 bits long. 

This highlights a fact about programming with functions: Most Machines are optimized for manipulating state instead of evaluating functional expressions. In fact, specialized machines had to be built to run LISP code efficiently. These LISP machines have a deep history in the domain of artificial intelligence, as they were capable of dealing effectively with symbolic systems.

## General Recursion

Now that we have lists, we might want to write some functions that operate on lists. For instance, given a particular list, we might be interested in the length of the list. Here is a naïve definition of such a function:
```
naïveLength = λlist.(isNil list) 0 (add 1 (naïveLength (tail list)))
```
This function uses the fact that the boolean that is returned by `(isNil list)` will choose between the `0` and the `(add 1 (naïveLength (tail list)))` branches of our function. Thus an empty list will have a length of 0, and any other list will have a length of 1 + the length of the rest of the list.

There is only one problem with the function I have given above: we use `naïveLength` in it's own definition, but we haven't actually defined `naïveLength` yet (since we are in the process of defining it). We will have to parameterize `naïveLength` as a function, which we can then pass to itself:
```
length' = λf.λlist.(isNil list) 0 (add 1 (f (tail list)))
``` 
So all we need to do to call `length'` is to feed it to itself:
```
length' length' = λf.λlist.(isNil list) 0 (add 1 (f (tail list))) length'
              = λlist.(isNil list) 0 (add 1 (length' (tail list)))
```
Only one problem: we've modified length so that it needs to be called on itself. so the inner most `length'` in `λlist.(isNil list) 0 (add 1 (length' (tail list)))` doesn't actually work, since it expects itself as it's first argument, not the list `(tail list)`. We seem to be stuck, so we will have to explore the fringes of the language to find a solution.

#### Breaking Lambda Calculus

What we need is a function which calls itself indefinitely. Since we know that our length function only calls itself when we haven't reached the base case of `nil`, we know we won't actually continue going forever, so long as we are given a list that ends. The simplest function that calls itself I can imagine is the following: 
```
omega = λx.x x
```
It takes one parameter and calls it on itself, what happens when we try to call `omega` on itself?
```
omega omega = (λx.x x) omega
            = omega omega
            = (λx.x x) omega
            .
            .
```
Wait a minute. This function doesn't seem to reduce. It's sort of like an infinite loop. So far, we've talked about functions that behave nicely, and return valid outputs for valid inputs. This is the first time we've looked at something that doesn't seem to give an answer.

Can we leverage this recursive behavior to make a recursive `length'` function? lets add omega to our length function:
```
length = omega (λf.λlist.(isNil list) 0 (add 1 (omega f (tail list))))
```
we know that omega just duplicates it's input, so we can rewrite length without the omegas:
```
length = (λf.λlist.(isNil list) 0 (add 1 (f f (tail list))))
         (λf.λlist.(isNil list) 0 (add 1 (f f (tail list))))
```
reducing `length` results in this:
```
length = (λf.λlist.(isNil list) 0 (add 1 (f f (tail list))))
         (λf.λlist.(isNil list) 0 (add 1 (f f (tail list))))
       = λlist.(isNil list) 0 
                            (add 1 
                                 (
                                   (λf.λlist.(isNil list) 0 (add 1 (f f (tail list)))) 
                                   (λf.λlist.(isNil list) 0 (add 1 (f f (tail list)))) 
                                   (tail list)
                                 )
                            )
```
Notice that length reduces to a function that expects a list now, instead of a function that expects itself. This allows us to call it on itself to get a function. In fact, we can see that by using referential transparency, we can derive our original definition of `naïveLength`:
```
length = (λf.λlist.(isNil list) 0 (add 1 (f f (tail list))))
         (λf.λlist.(isNil list) 0 (add 1 (f f (tail list))))
       = λlist.(isNil list) 0 
                            (add 1 
                                 (
                                   (λf.λlist.(isNil list) 0 (add 1 (f f (tail list)))) 
                                   (λf.λlist.(isNil list) 0 (add 1 (f f (tail list)))) 
                                   (tail list)
                                 )
                            )

       = λlist.(isNil list) 0 
                            (add 1 
                                 (
                                   length
                                   (tail list)
                                 )
                            )

length = λlist.(isNil list) 0 (add 1 (length (tail list)))
```

We can think about trying to write recursive functions more generally as creating a function that calls itself on itself. This is the property of a fixed point in mathematics. A fixed point is a value that when applied to a function, returns itself:
```
x = f x, where x is a fixed point of x
```
an example of a fixed point you are familiar with is the value `1` for the mathematical function `f(x) = x^2`, since `f(1) = 1`

If we want `x` to be a the fixed point of `f` we can write this as `x = Fix f`, where `Fix` is a function that takes an `f` and returns it's fixed point `x`:
```
Fix f = f (Fix f) = f (f (Fix f)) = f (f (f (Fix f)))
```
As you can see, a sufficient definition of the `Fix` function would give us recursion on the function `f`. The first fixed point function with this behavior was invented by Haskell Curry:
```
Y = λf.(λx.f(x x))(λx.f(x x))
```
Since Curry was a combinatorial logician, he called this function the "Y combinator". In general all you need to implement recursion in Lambda Calculus is to define a function that takes itself as an argument (like `length'`), and apply the Y combinator to it:
```
Y length' = λf.(λx.f(x x))(λx.f(x x)) length'
          = (λx.length' (x x))(λx. length' (x x))
          = length' ((λx.length' (x x))(λx. length' (x x)))
          = (λf.λlist.(isNil list) 0 (add 1 (f (tail list)))) 
            ((λx.length' (x x))
            (λx. length' (x x)))
          = λlist.(isNil list) 0 
                               (add 1 
                                   ((λx.length' (x x))
                                    (λx.length' (x x))
                                   (tail list))
                               )
          = λlist.(isNil list) 0 
                               (add 1 
                                    ((Y length')
                                     (tail list))
                               ) 
``` 
Notice how similar this is to the definition of `length` we derived earlier. Infact putting them side by side yields a surprising result:
```
Y length' = λlist.(isNil list) 0 
                               (add 1 
                                    (
                                     (Y length')
                                     (tail list)
                                    )
                               )

length    = λlist.(isNil list) 0 
                               (add 1 
                                   (
                                     length
                                     (tail list)
                                   )
                               ) 
```
it must therefore follow that `length = Y length'`
### Infinite Data Structures and 'lazy evaluation'

We might also want functions that generate infinite lists.
-- talk about sets too.