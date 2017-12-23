
### Mathematical Functions

Functions are familar objects to mathematicians, thus it is no wonder that the first method of effective computabilty relies on them. A function can be seen as a relation between two sets of things, which maps an element from one set (the domain) to an element of another (the codomain). Sometimes these sets might be the same. In fact, most mathematical functions that you are familiar with are functions from the set of real numbers, to itself. 

When defining a function, sometimes we will write the name of the function, and then some parameters in parenthesis, followed by an equal sign, which denotes the body of the function.

The function: `h(x) = 5x^3 - 3x^2 + 4x - 1` denotes a polynomial function, which is a linear combination of arbitrary degree terms of some variable. 

You might see that when simply calling `h(4)`, we are expressing the result of computing a polynomial on a parameter of `4`. In a sense, mathematical functions have the useful property of hiding the complexity of computation as the body of the function itself. The computation of course, is as simple as replacing the `x` with a `4` and simplifying the resulting expression as much as possible.



### Lambda Calculus

Church made one insight that gave Lambda Calculus the power it has. He realized that functions can serve as mappings on the set of functions itself. This means that a function might be regarded as a parameter of another function, and might return another function as a result:

```Haskell
let t(x) = g where g(y) = x
```
In this example, then function `t` takes a parameter `x` and returns a function `g`, which returns the original parameter to `t`, `x`. You can consider `g` to be a constant function, since it ignores it's input, `y`, and returns the same value no matter what it's input is. These meta-functions, or __higher order functions__ allow us to represent any useful computation we might want.

To make this process more concise, Church used the `λ` to denote the creation of a function:
```Haskell
let i = λx.x
```
`i` is the identity function, it has one parameter, and returns that value.

The lambda denotes the beginning of the parameters of the function `i`. the `.` denotes the beginning of the body of the function. Here's a way to rewrite `t`:

```Haskell
let t = λx.(λy.x)
```

Remember that we are allowed to define functions that return other functions:

```Haskell
let f = λx.i
```
Like `g`, The function `f` ignores it's input and returns the identity function. 

Lambda calculus has a few other rules which are really simple:
* Everything in lambda calculus is a function, therefore everything in Lambda Calculus is also a higher order function.
* Since everything is a function and the only thing we can do with functions is apply them to arguments, we can simply denote function application by putting two functions next to each other. The expression ` f t` means "f applied to t" which is normally `f(t)`. 
* Our functions can only have one argument, but since we can return functions, this allows us to have functions of multiple arguments.

However, we will see that parenthesis in normal math can mean both function application and grouping, and since we will want to do both in lambda calculus, we will omit the role of parenthesis as function application to avoid ambiguity. We can use parenthesis to denote the order in which functions are applied. If we want to evaluate `a b c` as "b applied to c, then a applied to that" we can write the expression as `a (b c)`. This also means that function application is naturally left associative. As an example, the expression `a b c d` means "a applied to b then applied to c then applied to d" which is also the same as `(((a b) c) d)`.
Lets see examples of how we might evaluate these functions:

```
f t f = (λx.i) t f
      = i f
      = f
f f t = (λx.i) f t
      = i t
      = t
```
So the function `f` takes one argument and returns a function that also takes an argument. In a sense, `f` is a function of two arguments, it can be applied to two parameters and return the latter.
```
t t f = (λx.(λy.x)) t f
      = (λy.t) f
      = t
t f t = (λx.(λy.x)) f t
      = (λy.f) t
      = f
```
The function `t` does the exact opposite of `f`, it takes two arguments and returns the former. 

In a sense, these two functions encode a choice between two options. This is the essence of what a boolean does, hence why I called these functions t and f (true and false). Church described encodings between mathematical objects (such as booleans), and functions in the lambda calculus to prove that lambda calculus could represent any computation you might want to express. For instance here are some example implementations of logical operations in lambda calculus:

```
not = (λa.a f t)
and = (λa.(λb.a b f))
or  = (λa.(λb.a t b))
```

You may have caught on to something weird I said earlier: "Everything in lambda calculus is a function". You might be wondering how we could represent numbers. Like booleans, Church defined an encoding of numbers as well:
```
0 = λf.λx.x
1 = λf.λx.f x
2 = λf.λx.f (f x)
3 = λf.λx.f (f (f x))
4 = λf.λx.f (f (f (f x)))
.
.
```
By representing the numbers as repeated composition, it makes it possible to define the mathematical operators on numbers. We can start with the increment (or successor) function, which returns the next number:
```
succ = λn.λf. f (n f) 
```
The way this function works might be a bit confusing, you may want to work through why this works. An even scarier function to define is the decrement or predecessor function. This returns the previous number:
```
pred = λn.λf.λx.n (λg.λh.h (g f)) (λy.x) I
```
I wouldn't recommend trying to make too much sense of it other than to remember that it is the same as `pred(x) = x - 1` in normal mathematics. Since our numbers are simply repeated function composition, we can write arithmetic operations like so:
```
add = λn.λm. (n succ) m
```
addition just takes two numbers, and calls the `succ` function on m n many times. Subtraction is very similar:
```
sub = λn.λm. (m pred) n
```
When we assume that n and m are number functions, they expect two parameters. At the point where we call `(m pred)`, we haven't given `m` all of the arguments that it needs. This is called partial application, and it can be useful for defining functions without dealing with real values. programming that deals with real values is called point free programming
