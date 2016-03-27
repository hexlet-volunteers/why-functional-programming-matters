# 3 Композиция функций

The first of the two new kinds of glue enables simple functions to be glued
together to make more complex ones. It can be illustrated with a simple listprocessing problem — adding the elements of a list. We can define lists by

Первый из двух новых видов соединений функций, позволяет функциям образовывать более сложные. Это можно увидеть на примере работы с простым списком -- добавления элементов в список. Мы можем определять списки как

```image
```

which means that a list of ∗s (whatever ∗ is) is either Nil , representing a list with no elements, or a Cons of a ∗ and another list of ∗s. A Cons represents a list whose first element is the ∗ and whose second and subsequent elements are the elements of the other list of ∗s. Here ∗ may stand for any type — for example, if ∗ is “integer” then the definition says that a list of integers is either empty or a Cons of an integer and another list of integers. Following normal practice, we will write down lists simply by enclosing their elements in square brackets, rather than by writing Conses and Nil s explicitly. This is simply a shorthand for notational convenience. For example,

что означает, что список ∗s (независимо от ∗) - это либо `Nil`, представляющий собой список без элементов, или `Cons` для `∗` и другого списка `∗s`. `Cons` является списком, первый элемент которого `∗`, а второй и последующие элементы являются элементами списка `∗s`. Здесь `∗` может обозначать любой тип данных - например, если `∗` является "целым числом", то определение говорит, что список целых чисел либо пуст, либо является `Cons` (соединением) целого числа и другого списка целых чисел. Следуя принятым обозначениям, мы будем записывать списки просто заключив их элементы в квадратные скобки, не используя `Cons` и `Nil` в явном виде. Это просто сокращение для удобства записи. Например,

```
[ ] то же самое, что и Nil
[1] то же самое, что и Cons 1 Nil
[1, 2, 3] то же самое, что и Cons 1 (Cons 2 (Cons 3 Nil))
```

The elements of a list can be added by a recursive function sum. The function
sum must be defined for two kinds of argument: an empty list (Nil ), and a
Cons. Since the sum of no numbers is zero, we define

Элементы списка могут быть сложены с помощью рекурсивной функции `sum`. Функция должна быть определена для двух аргументов: для пустого списка (`Nil`) и для `Cons`. Так как сумма нуля чисел равна нулю мы определяем

```
sum Nil = 0
```

and since the sum of a Cons can be calculated by adding the first element of
the list to the sum of the others, we can define

и сумма `Cons` может быть вычислена путем добавления первого элемента
списка к сумме других, мы можем определить

```
sum (Cons n list) = num + sum list
```

Examining this definition, we see that only the boxed parts below are specific
to computing a sum.

Посмотрев на это определение, можно видеть, что только в первой части приведены конкретные действия для вычисления суммы.

```
sum Nil = 0
sum (Cons n list) = n + sum list
```

This means that the computation of a sum can be modularized by gluing
together a general recursive pattern and the boxed parts. This recursive pattern is conventionally called foldr and so sum can be expressed as

This means that the computation of a sum can be modularized by gluing
together a general recursive pattern and the boxed parts. Этот рекурсивный шаблон условно называется `foldr` и поэтому сумма может быть выражена как

```
sum = foldr (+) 0
```

The definition of foldr can be derived just by parameterizing the definition of
sum, giving

Определение `foldr` может быть получено путем параметризации определения функции `sum` следующим образом

```
(foldr f x) Nil = x
(foldr f x) (Cons a l ) = f a ((foldr f x) l )
```

Here we have written brackets around (foldr f x) to make it clear that it replaces sum. Conventionally the brackets are omitted, and so ((foldr f x) l) is written as (foldr f x l). A function of three arguments such as foldr, applied to only two, is taken to be a function of the one remaining argument, and in general, a function of n arguments applied to only m of them (m < n) is taken to be a function of the n − m remaining ones. We will follow this convention in future.

Здесь мы пришем скобки вокруг `(foldr f x)` чтобы более ясно показать, что это заменяет функцию `sum`. Скобки могут быть опущены, и `((foldr f x) l)` может быть записано как `(foldr f x l)`. Функции трех аргументов, такие как `foldr`, применяются к двум рагументам, а затем получается функция от одного аргумента,  и в общем случае, функция `n` аргументов, которая применяется к `m` аргументам, где m < n дает функцию  от `n − m` оставшихся аргументов.

Having modularized sum in this way, we can reap benefits by reusing the
parts. The most interesting part is foldr, which can be used to write down a
function for multiplying together the elements of a list with no further programming:

Записав функцию `sum` в таком виде, мы можем получить выгодную возможность переиспользования частей определения. Самое интересное, что `foldr` может использоваться, для записи функции умножения элементов списка без дальнейшей доработки: 

```
product = foldr (∗) 1
```

It can also be used to test whether any of a list of booleans is true

Можно так же использовать `foldr` проверки того, является ли хотя бы одно значение списка булевых значений истинным 

```
anytrue = foldr (∨) F alse
```

or whether they are all true

или являются ли все значения истинными

```
alltrue = foldr (∧) True
```

One way to understand (foldr f a) is as a function that replaces all occurrences of Cons in a list by f , and all occurrences of Nil by a. Taking the list [1, 2, 3] as an example, since this means

Один из способов понять `(foldr f a)` как функцию  состоит в том, чтобы заменить все вхождения `Cons` в списке на `f` , а все вхождения `Nil` на `a`. Например, [1, 2, 3] будет обозначать то же, что и

```
Cons 1 (Cons 2 (Cons 3 Nil ))
```

then (foldr (+) 0) converts it into

тогда `(foldr (+) 0)` преобразуется в

```
(+) 1 ((+) 2 ((+) 3 0)) = 6
```

и `(foldr (∗) 1)` преобразуется в

```
(∗) 1 ((∗) 2 ((∗) 3 1)) = 6
```

Now it’s obvious that (foldr Cons Nil ) just copies a list. Since one list can be appended to another by Cons ing its elements onto the front, we find

Теперь очевидно, что `(foldr Cons Nil)` просто копирует список. Так как список ещё может быть расширен путем добавления новых элементов в голову с помощью `Cons`, то мы определяем

```
append a b = foldr Cons b a
```

As an example,

Например,

```
append [1, 2] [3, 4] = foldr Cons [3, 4] [1, 2]
= foldr Cons [3, 4] (Cons 1 (Cons 2 Nil ))
= Cons 1 (Cons 2 [3, 4]))
(replacing Cons by Cons and Nil by [3, 4])
= [1, 2, 3, 4]
```

We can count the number of elements in a list using the function length, defined by

Мы можем подсчитать количество элементов в списке с помощью функции `length`, определяемой как

``
length = foldr count 0
count a n = n + 1
``

because count increments 0 as many times as there are Cons es. A function that
doubles all the elements of a list could be written as

потому что `count` добавляет единицу к нулю столько раз, сколько элементов присутствует в списке. Функция, которая дублирует элементы списка может быть записана как

```
doubleall = foldr doubleandcons Nil
```

where

где

```
doubleandcons n list = Cons (2 ∗ n) list
```

The function doubleandcons can be modularized even further, first into

Функция `doubleandcons` может быть обобщена ещё больше

```
doubleandcons = f andcons double
```

where

где

```
double n = 2 ∗ n
f andcons f el list = Cons (f el ) list
```

and then by

и тогда поскольку

```
f andcons f = Cons . f
```

where “.” (function composition, a standard operator) is defined by

где “.” (функция композиции, стандартный оператор) определенная как

```
(f . g) h = f (g h)
```

We can see that the new definition of f andcons is correct by applying it to some arguments:

Мы можем видеть, что новое определение `f andcons` будет правильным  будучи примененным к некоторым аргументам:

```
f andcons f el

= (Cons . f ) el
= Cons (f el )
```

so

так

```
f andcons f el list = Cons (f el ) list
```

The final version is

Финальная версия

```
doubleall = foldr (Cons . double) Nil
```

With one further modularization we arrive at

```
doubleall = map double
map f = foldr (Cons . f ) Nil
```

where map — another generally useful function — applies any function f to all
the elements of a list.

We can even write a function to add all the elements of a matrix, represented
as a list of lists. It is

```
summatrix = sum . map sum
```

The function map sum uses sum to add up all the rows, and then the leftmost
sum adds up the row totals to get the sum of the whole matrix.
These examples should be enough to convince the reader that a little modularization can go a long way. By modularizing a simple function (sum) as a
combination of a “higher-order function” and some simple arguments, we have
arrived at a part (f oldr) that can be used to write many other functions on lists
with no more programming effort.
We do not need to stop with functions on lists. As another example, consider
the datatype of ordered labeled trees, defined by

```
treeof ∗ ::= Node ∗ (listof (treeof ∗))
```

This definition says that a tree of ∗s is a node, with a label which is a ∗, and a
list of subtrees which are also trees of ∗s. For example, the tree
```image
```
would be represented by

```
Node 1
(Cons (Node 2 Nil )
(Cons (Node 3
(Cons (Node 4 Nil ) Nil ))
Nil ))
```

Instead of considering an example and abstracting a higher-order function from
it, we will go straight to a function foldtree analogous to foldr. Recall that foldr
took two arguments: something to replace Cons with and something to replace
Nil with. Since trees are built using Node, Cons, and Nil , foldtree must take
three arguments — something to replace each of these with. Therefore we define

```
foldtree f g a (Node label subtrees) =
f label (foldtree f g a subtrees)
foldtree f g a (Cons subtree rest) =
g (foldtree f g a subtree) (foldtree f g a rest)
foldtree f g a Nil = a
```

Many interesting functions can be defined by gluing foldtree and other functions
together. For example, all the labels in a tree of numbers can be added together
using

```
sumtree = foldtree (+) (+) 0
```

Taking the tree we wrote down earlier as an example, sumtree gives

```
(+) 1
((+) ((+) 2 0)
((+) ((+) 3
((+) ((+) 4 0) 0))
0))
= 10
```

A list of all the labels in a tree can be computed using

labels = foldtree Cons append Nil

The same example gives

```
Cons 1
(append (Cons 2 Nil )
(append (Cons 3
(append (Cons 4 Nil ) Nil ))
Nil ))
= [1, 2, 3, 4]
```

Finally, one can define a function analogous to map which applies a function f
to all the labels in a tree:

```
maptree f = foldtree (Node . f ) Cons Nil
```

All this can be achieved because functional languages allow functions that are
indivisible in conventional programming languages to be expressed as a combinations of parts — a general higher-order function and some particular specializing
functions. Once defined, such higher-order functions allow many operations to
be programmed very easily. Whenever a new datatype is defined, higher-order
functions should be written for processing it. This makes manipulating the
datatype easy, and it also localizes knowledge about the details of its representation. The best analogy with conventional programming is with extensible
languages — in effect, the programming language can be extended with new
control structures whenever desired.
