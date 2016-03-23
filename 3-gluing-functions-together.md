# 3 Gluing functions together

The first of the two new kinds of glue enables simple functions to be glued
together to make more complex ones. It can be illustrated with a simple listprocessing problem — adding the elements of a list. We can define lists by
```image
```
which means that a list of ∗s (whatever ∗ is) is either Nil , representing a list
with no elements, or a Cons of a ∗ and another list of ∗s. A Cons represents
a list whose first element is the ∗ and whose second and subsequent elements
are the elements of the other list of ∗s. Here ∗ may stand for any type — for
example, if ∗ is “integer” then the definition says that a list of integers is either
empty or a Cons of an integer and another list of integers. Following normal
practice, we will write down lists simply by enclosing their elements in square
brackets, rather than by writing Conses and Nil s explicitly. This is simply a
shorthand for notational convenience. For example,

[ ] means Nil
[1] means Cons 1 Nil
[1, 2, 3] means Cons 1 (Cons 2 (Cons 3 Nil))

The elements of a list can be added by a recursive function sum. The function
sum must be defined for two kinds of argument: an empty list (Nil ), and a
Cons. Since the sum of no numbers is zero, we define

sum Nil = 0

and since the sum of a Cons can be calculated by adding the first element of
the list to the sum of the others, we can define

sum (Cons n list) = num + sum list

Examining this definition, we see that only the boxed parts below are specific
to computing a sum.

sum Nil = 0
sum (Cons n list) = n + sum list

This means that the computation of a sum can be modularized by gluing
together a general recursive pattern and the boxed parts. This recursive pattern
is conventionally called foldr and so sum can be expressed as

sum = foldr (+) 0

The definition of foldr can be derived just by parameterizing the definition of
sum, giving

(foldr f x) Nil = x
(foldr f x) (Cons a l ) = f a ((foldr f x) l )

Here we have written brackets around (foldr f x) to make it clear that it replaces
sum. Conventionally the brackets are omitted, and so ((foldr f x) l) is written
as (foldr f x l). A function of three arguments such as foldr, applied to only
two, is taken to be a function of the one remaining argument, and in general,
a function of n arguments applied to only m of them (m < n) is taken to be a
function of the n − m remaining ones. We will follow this convention in future.
Having modularized sum in this way, we can reap benefits by reusing the
parts. The most interesting part is foldr, which can be used to write down a
function for multiplying together the elements of a list with no further programming:

product = foldr (∗) 1

It can also be used to test whether any of a list of booleans is true

anytrue = foldr (∨) F alse

or whether they are all true

alltrue = foldr (∧) True

One way to understand (foldr f a) is as a function that replaces all occurrences
of Cons in a list by f , and all occurrences of Nil by a. Taking the list [1, 2, 3]
as an example, since this means

Cons 1 (Cons 2 (Cons 3 Nil ))

then (foldr (+) 0) converts it into

(+) 1 ((+) 2 ((+) 3 0)) = 6

and (foldr (∗) 1) converts it into

(∗) 1 ((∗) 2 ((∗) 3 1)) = 6

Now it’s obvious that (foldr Cons Nil ) just copies a list. Since one list can be
appended to another by Cons ing its elements onto the front, we find

append a b = foldr Cons b a

As an example,

append [1, 2] [3, 4] = foldr Cons [3, 4] [1, 2]
= foldr Cons [3, 4] (Cons 1 (Cons 2 Nil ))
= Cons 1 (Cons 2 [3, 4]))
(replacing Cons by Cons and Nil by [3, 4])
= [1, 2, 3, 4]

We can count the number of elements in a list using the function length, defined
by

length = foldr count 0
count a n = n + 1

because count increments 0 as many times as there are Cons es. A function that
doubles all the elements of a list could be written as

doubleall = foldr doubleandcons Nil

where

doubleandcons n list = Cons (2 ∗ n) list

The function doubleandcons can be modularized even further, first into

doubleandcons = f andcons double

where

double n = 2 ∗ n
f andcons f el list = Cons (f el ) list

and then by

f andcons f = Cons . f

where “.” (function composition, a standard operator) is defined by

(f . g) h = f (g h)

We can see that the new definition of f andcons is correct by applying it to some
arguments:

f andcons f el

= (Cons . f ) el
= Cons (f el )

so

f andcons f el list = Cons (f el ) list

The final version is

doubleall = foldr (Cons . double) Nil

With one further modularization we arrive at

doubleall = map double
map f = foldr (Cons . f ) Nil

where map — another generally useful function — applies any function f to all
the elements of a list.

We can even write a function to add all the elements of a matrix, represented
as a list of lists. It is

summatrix = sum . map sum

The function map sum uses sum to add up all the rows, and then the leftmost
sum adds up the row totals to get the sum of the whole matrix.
These examples should be enough to convince the reader that a little modularization can go a long way. By modularizing a simple function (sum) as a
combination of a “higher-order function” and some simple arguments, we have
arrived at a part (f oldr) that can be used to write many other functions on lists
with no more programming effort.
We do not need to stop with functions on lists. As another example, consider
the datatype of ordered labeled trees, defined by

treeof ∗ ::= Node ∗ (listof (treeof ∗))

This definition says that a tree of ∗s is a node, with a label which is a ∗, and a
list of subtrees which are also trees of ∗s. For example, the tree
```image```
would be represented by

Node 1
(Cons (Node 2 Nil )
(Cons (Node 3
(Cons (Node 4 Nil ) Nil ))
Nil ))

Instead of considering an example and abstracting a higher-order function from
it, we will go straight to a function foldtree analogous to foldr. Recall that foldr
took two arguments: something to replace Cons with and something to replace
Nil with. Since trees are built using Node, Cons, and Nil , foldtree must take
three arguments — something to replace each of these with. Therefore we define

foldtree f g a (Node label subtrees) =
f label (foldtree f g a subtrees)
foldtree f g a (Cons subtree rest) =
g (foldtree f g a subtree) (foldtree f g a rest)
foldtree f g a Nil = a

Many interesting functions can be defined by gluing foldtree and other functions
together. For example, all the labels in a tree of numbers can be added together
using

sumtree = foldtree (+) (+) 0

Taking the tree we wrote down earlier as an example, sumtree gives

(+) 1
((+) ((+) 2 0)
((+) ((+) 3
((+) ((+) 4 0) 0))
0))
= 10

A list of all the labels in a tree can be computed using

labels = foldtree Cons append Nil

The same example gives

Cons 1
(append (Cons 2 Nil )
(append (Cons 3
(append (Cons 4 Nil ) Nil ))
Nil ))
= [1, 2, 3, 4]

Finally, one can define a function analogous to map which applies a function f
to all the labels in a tree:

maptree f = foldtree (Node . f ) Cons Nil

All this can be achieved because functional languages allow functions that are
indivisible in conventional programming languages to be expressed as a combinations of parts — a general higher-order function and some particular specializing
functions. Once defined, such higher-order functions allow many operations to
be programmed very easily. Whenever a new datatype is defined, higher-order
functions should be written for processing it. This makes manipulating the
datatype easy, and it also localizes knowledge about the details of its representation. The best analogy with conventional programming is with extensible
languages — in effect, the programming language can be extended with new
control structures whenever desired.

