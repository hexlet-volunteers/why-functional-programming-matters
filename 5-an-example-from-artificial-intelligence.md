# 5. An Example from Artificial Intelligence

# 5. Пример из области искусственного интеллекта

We have argued that functional languages are powerful primarily because they
provide two new kinds of glue: higher-order functions and lazy evaluation. In
this section we take a larger example from Artificial Intelligence and show how
it can be programmed quite simply using these two kinds of glue.
The example we choose is the alpha-beta “heuristic”, an algorithm for estimating how good a position a game-player is in. The algorithm works by looking
ahead to see how the game might develop, but it avoids pursuing unprofitable
lines.

Ранее мы утверждали, что функциональные языки являются мощными в первую очередь потому, что они
обеспечивают два новых типа соединения: функции высших порядков и ленивые вычисления.
В данной главе мы разберем более сложную задачу из области искусственного интеллекта и покажем, как
достаточно просто она может быть запрограммирована с их помощью.
Для примера мы выберем алгоритм альфа-бета-отсечения "эвристика", который позволяет определить насколько хороша позиция игрока в игре. Алгоритм пытается просчитать варианты развития игры,
избегая неперспективных направлений.


Let game positions be represented by objects of the type position. This type
will vary from game to game, and we assume nothing about it. There must be
some way of knowing what moves can be made from a position: Assume that
there is a function,

Пусть позиции в игре будут представлены объектами типа position. Этот тип
будет варьироваться от игры к игре и мы пока ничего не знаем об этом. Должен быть
какой-нибудь способ узнать какие ходы могут быть сделаны из некоторой position:
предположим, что есть функция,

moves :: position → listof position

that takes a game-position as its argument and returns the list of all positions
that can be reached from it in one move. As an example, Fig. 1 shows moves for
a couple of positions in tic-tac-toe (noughts and crosses). This assumes that it
is always possible to tell which player’s turn it is from a position. In tic-tac-toe
this can be done by counting the ×s and Os; in a game like chess one would have
to include the information explicitly in the type position.

которая берет position в качестве аргумента и возвращает список всех position
за один ход. В качестве примера на рис. 1 показаны ходы для
нескольких position в tic-tac-toe (крестики и нолики). Из position всегда можно
предположить какого игрока очередь ходить. В этой игре это может быть сделано
путем подсчета количества крестиков и ноликов, а такой игре как шахматы можно было бы
включить эту информацию непосредственно в тип position.


Given the function moves, the first step is to build a game tree. This is a
tree in which the nodes are labeled by positions, so that the children of a node
are labeled with the positions that can be reached in one move from that node.
That is, if a node is labeled with position p, then its children are labeled with
the positions in (moves p). Game trees are not all finite: If it’s possible for a
game to go on forever with neither side winning, its game tree is infinite. Game
trees are exactly like the trees we discussed in Section 2 — each node has a

Учитывая функцию ходов, первый шаг заключается в создании игрового дерева. Это
дерево, в котором узлы помечены position так, что потомки некоторого узла
помечены position, которые могут быть достигнуты за один ход из этого узла.
То есть, если узел помечен position р, то его потомки помечены
positions in (ходит p). Не все игровые деревья имеют предел: если возможно для
игры продолжаться вечно без победы ни одной из сторон, то ее игровое дерево бесконечно. Игровые
деревья точно такие же как деревья, которые мы обсуждали в главе 2 - каждый узел имеет


Figure 1: moves for two positions in tic-tac-toe.
Figure 2: Part of a game tree for tic-tac-toe.

label (the position it represents) and a list of subnodes. We can therefore use
the same datatype to represent them.

метку (представляет position) и список подузлов. Поэтому мы можем использовать
тот же тип данных для их представления.

A game tree is built by repeated applications of moves. Starting from the
root position, moves is used to generate the labels for the subtrees of the root.
It is then used again to generate the subtrees of the subtrees and so on. This
pattern of recursion can be expressed as a higher-order function,

Игровое дерево выстраивается путем повторяющихся применений ходов. Начиная от
корневой позиции, ходы используется для генерации меток для поддеревьев корня.
Затем они используется снова для создания поддеревьев поддеревьев и так далее. Этот
образец рекурсии может быть выражен в виде функции высшего порядка,


reptree f a = Node a (map (reptree f ) (f a))

Using this function another can be defined which constructs a game tree from
a particular position:

С помощью этой функции можно определить другую функцию, которая строит игровое дерево от
конкретного положения:

gametree p = reptree moves p

As an example, consider Fig. 2. The higher-order function used here (reptree) is
analogous to the function repeat used to construct infinite lists in the preceding
section.

В качестве примера, рассмотрим рис. 2. Используемая здесь функция высшего порядка (reptree)
является аналогичной функции, которая используется для построения бесконечных списков
в предшествующем разделе.

The alpha-beta algorithm looks ahead from a given position to see whether
the game will develop favorably or unfavorably, but in order to do so it must be
able to make a rough estimate of the value of a position without looking ahead.
This “static evaluation” must be used at the limit of the look-ahead, and may
be used to guide the algorithm earlier. The result of the static evaluation
is a measure of the promise of a position from the computer’s point of view
(assuming that the computer is playing the game against a human opponent).
The larger the result, the better the position for the computer. The smaller the
result, the worse the position. The simplest such function would return (say)
1 for positions where the computer has already won, −1 for positions where
the computer has already lost, and 0 otherwise. In reality, the static evaluation
function measures various things that make a position “look good”, for example
material advantage and control of the center in chess. Assume that we have
such a function,

Альфа-бета алгоритм смотрит вперед от заданной позиции, чтобы понять, будет ли
игра развиваться благоприятно или неблагоприятно, но для того чтобы сделать это он должен быть
в состоянии сделать грубую оценку значения позиции, не глядя вперед.
Эта "статическая оценка" должна быть применена на границе прогноза и может быть
использована для ориентирования алгоритма на ранней стадии. Результат статической оценки
является мерой перспективности состояния с точки зрения компьютера
(при условии, что компьютер играет в игру против человека).
Чем значительнее результат, тем лучше состояние для компьютера. Чем незначительнее
результат, тем хуже состояние. В простейшем случае такая функция будет возвращать (скажем)
1 для состояния, где компьютер уже победил, -1 для состояния где
компьютер уже проиграл, и 0 в противном случае. На самом же деле, функция статической оценки
измеряет различные вещи, которые составляют состояние "выглядит хорошо", например,
материальный перевес и контроль центра в шахматах. Предположим, что мы имеем
такую функцию,


static :: position → number

Since a game tree is a (treeof position), it can be converted into a (treeof number)
by the function (maptree static), which statically evaluates all the positions in
the tree (which may be infinitely many). This uses the function maptree defined
in Section 2.

Поскольку игровое дерево является (treeof position), оно может быть превращено в
(treeof number) с помощью функции (maptree static), которая статически оценивает все
позиции в этом дереве (которых может быть бесконечно много). При этом используется
функция maptree, определенная в главе 2.


Given such a tree of static evaluations, what is the true value of the positions
in it? In particular, what value should be ascribed to the root position? Not
its static value, since this is only a rough guess. The value ascribed to a node
must be determined from the true values of its subnodes. This can be done by
assuming that each player makes the best moves possible. Remembering that
a high value means a good position for the computer, it is clear that when it is
the computer’s move from any position, it will choose the move leading to the
subnode with the maximum true value. Similarly, the opponent will choose the
move leading to the subnode with the minimum true value. Assuming that the
computer and its opponent alternate turns, the true value of a node is computed
by the function maximize if it is the computer’s turn and minimize if it is not:

Учитывая такое дерево статических оценок, какова истинная ценность позиций
в нем? В частности, какая величина должна быть назначена корневой позиции? Не
ее статическое значение, так как это только грубое предположение. Величина, назначенная узлу,
должна быть определена из истинных значений его подузлов. Это может быть сделано предположив,
что каждый игрок делает лучшие ходы. Не забывая, что
высокое значение означает хорошее состояние для компьютера, очевидно, что в свою очередь из любой
позиции он выберет ход, ведущий в подузел с максимальным истинным значением.
Аналогичным образом, противник выберет ход, ведущий в подузел с минимальным истинным значением. Если предположить, что компьютер и его противник чередуют ходы, истинное значение узла вычисляется
функцией maximize в случае хода компьютера и minimize в противном случае:


maximize (Node n sub) = max (map minimize sub)
minimize (Node n sub) = min (map maximize sub)

Here max and min are functions on lists of numbers that return the maximum
and minimum of the list respectively. These definitions are not complete because
they recurse forever — there is no base case. We must define the value of a node
with no successors, and we take it to be the static evaluation of the node (its
label). Therefore the static evaluation is used when either player has already
won, or at the limit of look-ahead. The complete definitions of maximize and
minimize are

Здесь max и min это функции списков чисел, которые возвращают максимум
и минимум списка соответственно. Эти определения не являются полными, потому что
они всегда рекурсивны - не существует базового случая. Мы должны определить значение узла
без каких-либо потомков, и мы принимаем, что это будет статическая оценка узла (его
метка). Поэтому статическая оценка используется, когда один из игроков уже или
выиграл, или на пределе прогноза. Полные определения maximize и
minimize являются


maximize (Node n Nil ) = n
maximize (Node n sub) = max (map minimize sub)
minimize (Node n Nil ) = n
minimize (Node n sub) = max (map maximize sub)

One could almost write a function at this stage that would take a position and
return its true value. This would be:

На данном этапе можно было бы фактически написать функцию, которая брала некоторую позицию и
возвращала ее истинное значение. Это было бы:


evaluate = maximize . maptree static . gametree

There are two problems with this definition. First of all, it doesn’t work for
infinite trees, because maximize keeps on recursing until it finds a node with
no subtrees — an end to the tree. If there is no end then maximize will return
no result. The second problem is related — even finite game trees (like the one
for tic-tac-toe) can be very large indeed. It is unrealistic to try to evaluate the
whole of the game tree — the search must be limited to the next few moves.
This can be done by pruning the tree to a fixed depth,

Есть две проблемы с этим определением. Прежде всего, это не работает для
бесконечных деревьев, потому что maximize выполняется рекурсивно до тех пор, пока не найдет узел без
поддеревьев - т.е. конец дерева. Если нет конца, то maximize не вернет
результат. Вторая проблема связана с тем, что даже игровые деревья с концом (как для игры
крестики-нолики) могут быть очень большими. Это нереально пытаться оценить
все игровое дерево - поиск должен быть ограничен несколькими последующими ходами.
Это может быть сделано путем отсечения дерева на фиксированную глубину,


prune 0 (Node a x)

= Node a Nil

prune (n + 1) (Node a x) = Node a (map (prune n) x)

The function (prune n) takes a tree and “cuts off” all nodes further than n from
the root. If a game tree is pruned it forces maximize to use the static evaluation
for nodes at depth n, instead of recursing further. The function evaluate can
therefore be defined by

Функция (prune n) принимает дерево и "отсекает" все узлы дальше чем n от
корня. Если игровое дерево отсечено, то это вынуждает maximize вместо рекурсии
использовать статическую оценку для узлов на глубине n. Поэтому функция evaluate может
быть определена как


evaluate = maximize . maptree static . prune 5 . gametree

which looks (say) five moves ahead.

которая ищет (скажем) на пять ходов вперед.

Already in this development we have used higher-order functions and lazy
evaluation. Higher-order functions reptree and maptree allow us to construct
and manipulate game trees with ease. More importantly, lazy evaluation permits
us to modularize evaluate in this way. Since gametree has a potentially infinite
result, this program would never terminate without lazy evaluation. Instead of
writing

Уже на этой стадии мы использовали функции высшего порядка и ленивые
вычисления. Функции высшего порядка reptree и maptree позволяют нам с легкостью строить
и манипулировать игровыми деревьями. Что еще более важно, таким образом ленивые вычисления
позволяют нам модуляризировать вычисдения. Так как gametree имеет потенциально бесконечный
результат, эта программа никогда не закончится без ленивых вычислений. Вместо этого
напишем


prune 5 . gametree

we would have to fold these two functions together into one that constructed
only the first five levels of the tree. Worse, even the first five levels may be too
large to be held in memory at one time. In the program we have written, the
function

мы должны были бы сложить вместе две эти функции в одну, которая построит
только первые пять уровней дерева. Хуже того, даже первые пять уровней могут занимать слишком
много памяти в одно время. В программе, которую мы написали,
функция


maptree static . prune 5 . gametree

constructs parts of the tree only as maximize requires them. Since each part
can be thrown away (reclaimed by the garbage collector) as soon as maximize
has finished with it, the whole tree is never resident in memory. Only a small
part of the tree is stored at a time. The lazy program is therefore efficient.
This efficiency depends on an interaction between maximize (the last function
in the chain of compositions) and gametree (the first); without lazy evaluation,
therefore, it could be achieved only by folding all the functions in the chain
together into one big one. This would be a drastic reduction in modularity,
but it is what is usually done. We can make improvements to this evaluation
algorithm by tinkering with each part; this is relatively easy. A conventional
programmer must modify the entire program as a unit, which is much harder.
So far we have described only simple minimaxing. The heart of the alphabeta algorithm is the observation that one can often compute the value returned
by maximize or minimize without looking at the whole tree. Consider the tree:

конструирует части дерева как только maximize нуждается в них. Как только maximize
завершает работу с каждой частью и она может быть отброшена (утилизирована сборщиком мусора)
все дерево никогда не находится в памяти. Только небольшая
часть дерева хранится одновременно. Поэтому ленивая программа эффективна.
Эта эффективность зависит от взаимодействия между maximize (последняя функция
в цепи композиций) и gametree (the first). Следовательно, без ленивых вычислений
это может быть достигнуто только путем складывания всех функций вместе по цепочке
в одну большую. Это было бы резкое снижение модульности,
но так это обычно и делается. Мы можем внести улучшения в этот
алгоритм вычисления повозившись с каждой частью - это относительно просто. Обычный же
программист должен изменить всю программу, что гораздо сложнее.
До сих пор мы описали только простые minimaxing. Суть алгоритма альфа-бета является наблюдение,
что часто можно вычислить значение, возвращаемое
maximize или minimize без прохода по всему дереву. Рассмотрим следующее дерево:


*image*

Strangely enough, it is unnecessary to know the value of the question mark
in order to evaluate the tree. The left minimum evaluates to 1, but the right
minimum clearly evaluates to something at most 0. Therefore the maximum of
the two minima must be 1. This observation can be generalized and built into
maximize and minimize.

Как ни странно, нет необходимости знать значение знака вопроса
для того, чтобы оценить дерево. Левый минимум принимает значение 1, а правый
минимум несомненно оценивает что-то не более 0. Поэтому максимум
двух минимумов должен быть равен 1. Это наблюдение можно обобщить и встроить в
maximize и minimize.

The first step is to separate maximize into an application of max to a list
of numbers; that is, we decompose maximize as

Первый шаг заключается в разделении maximize кв приложении max к списку
чисел; то есть, мы декомпозируем maximize, как

maximize = max . maximize0

(We decompose minimize in a similar way. Since minimize and maximize are
entirely symmetrical we shall discuss maximize and assume that minimize is
treated similarly.) Once decomposed in this way, maximize can use minimize0 ,
rather than minimize itself, to discover which numbers minimize would take
the minimum of. It may then be able to discard some of the numbers without
looking at them. Thanks to lazy evaluation, if maximize doesn’t look at all of
the list of numbers, some of them will not be computed, with a potential saving
in computer time.

(Мы декомпозируем minimize подобным же образом. Так как minimize и maximize являются
полностью симметричными мы обсудим maximize и предположим, что minimize
разбирается аналогично). После декомпозиции maximize может использовать minimize0,
чтобы выяснить какие числа минимизировать для нахождения минимального из них.
Затем она может быть в состоянии отказаться от некоторых из этих чисел без
их проверок. Если maximize не проверяет весь список чисел, то благодаря ленивым вычислениям
некоторые из них не будут вычисляться, что приведет к  потенциальной экономии
машинного времени.

It’s easy to “factor out” max from the definition of maximize, giving

Легко "вынести за скобки" максимум из определения maximize, давая

maximize0 (Node n Nil ) = Cons n Nil
maximize0 (Node n l )

= map minimize l
= map (min . minimize0 ) l
= map min (map minimize0 l )
= mapmin (map minimize0 l )
where mapmin = map min

Since minimize0 returns a list of numbers, the minimum of which is the result of
minimize, (map minimize0 l) returns a list of lists of numbers, and maximize0
should return a list of those lists’ minima. Only the maximum of this list
matters, however. We shall define a new version of mapmin that omits the
minima of lists whose minimum doesn’t matter.

Так как minimize0 возвращает список чисел, минимум которого является результатом
сminimize, (map minimize0 l) возвращает список списков чисел, и maximize0
должна возвращать список минимумов этих списков. Однако, важен только максимум этого списка.
Мы определим новую версию mapmin, которая не включает
минимумы списков чьи минимумы не имеет значения.


mapmin (Cons nums rest)
= Cons (min nums) (omit (min nums) rest)

The function omit is passed a “potential maximum” — the largest minimum
seen so far — and omits any minima that are less than this:

Функция omit пропускает "возможный максимум" - наибольший минимум
рассматриваемый до сих пор - и не включает любые минимумы, которые менше чем это:

omit pot Nil = Nil
omit pot (Cons nums rest)
= omit pot rest,

if minleq nums pot

= Cons (min nums) (omit (min nums) rest), otherwise

The function minleq takes a list of numbers and a potential maximum, and it
returns True if the minimum of the list of numbers does not exceed the potential
maximum. To do this, it does not need to look at the entire list! If there is
any element in the list less than or equal to the potential maximum, then the
minimum of the list is sure to be. All elements after this particular one are
irrelevant — they are like the question mark in the example above. Therefore

Функция minleq принимает список чисел и возможный максимум и
возвращает True если минимум списка чисел не превосходит возможный
максимум. Для этого ей не нужно проверять весь список! Если там есть
любой элемент в списке меньший или равный возможному максимуму, то
минимум списка обязательно будет. Все элементы после конкретно этого
не важны - они похожи на знак вопроса в приведенном выше примере. Следовательно


minleq can be defined by
minleq Nil pot = F alse
minleq (Cons n rest) pot = True,
= minleq rest pot,

if n ≤ pot
otherwise

Having defined maximize0 and minimize0 in this way it is simple to write a
new evaluator:

Определив таким образом maximize0 и minimize0 будет просто написать
новый оценщик:

evaluate = max . maximize0 . maptree static . prune 8 . gametree

Thanks to lazy evaluation, the fact that maximize0 looks at less of the tree
means that the whole program runs more efficiently, just as the fact that prune
looks at only part of an infinite tree enables the program to terminate. The
optimizations in maximize0 , although fairly simple, can have a dramatic effect
on the speed of evaluation and so can allow the evaluator to look further ahead.
Other optimizations can be made to the evaluator. For example, the alphabeta algorithm just described works best if the best moves are considered first,
since if one has found a very good move then there is no need to consider worse
moves, other than to demonstrate that the opponent has at least one good reply
to them. One might therefore wish to sort the subtrees at each node, putting
those with the highest values first when it is the computer’s move and those
with the lowest values first when it is not. This can be done with the function

Благодаря ленивым вычислениям тот факт, что maximize0 проверяет меньшую часть дерева
означает, что вся программа работает более эффективно, так же как и то, что prune
проверяет только часть бесконечного дерева, позволяющее программе завершиться.
Оптимизации в maximize0, хотя и довольно простой, могут иметь драматические последствия
по скорости вычисления и поэтому может позволить оценщику проверять дальше.
К оценщику можно применить другие оптимизации. Например, только что описанный алгоритм алфа-бета
работает наилучшим образом если самый удачный ход рассмотрен первым. Как только такой ход найден,
то нет необходимости рассматривать менее удачные ходы, кроме как для демонстрации что у оппонента
есть один хороший ответ на них.
Поэтому можно было бы отсортировать поддеревья в каждом узле: во время хода компьтера переместить
самымые большие значения на первые места и переместить самымые маленькие значения на
первые места во время хода оппонента. Это может быть сделано с помощью функции

highfirst (Node n sub) = Node n (sort higher (map lowfirst sub))
lowfirst (Node n sub) = Node n (sort (not . higher) (map highfirst sub))
higher (Node n1 sub1) (Node n2 sub2) = n1 > n2

where sort is a general-purpose sorting function. The evaluator would now be
defined by

где sort является функцией сортировки общего назначения. Оценщик будет теперь
определяется


evaluate
= max . maximize0 . highfirst . maptree static . prune 8 . gametree

One might regard it as sufficient to consider only the three best moves for the
computer or the opponent, in order to restrict the search. To program this, it
is necessary only to replace highfirst with (taketree 3 . highfirst), where

С тем чтобы ограничить поиск можно было бы относительно этого рассмотреть только три лучших хода для компьютера или противника. Чтобы запрограммировать это,
необходимо только заменить highfirst с (taketree 3 . highfirst), где


taketree n = foldtree (nodett n) Cons Nil
nodett n label sub = Node label (take n sub)

The function taketree replaces all the nodes in a tree with nodes that have at
most n subnodes, using the function (take n), which returns the first n elements
of a list (or fewer if the list is shorter than n).
Another improvement is to refine the pruning. The program above looks
ahead a fixed depth even if the position is very dynamic — it may decide to
look no further than a position in which the queen is threated in chess, for
example. It’s usual to define certain “dynamic” positions and not to allow lookahead to stop in one of these. Assuming a function dynamic that recognizes
such positions, we need only add one equation to prune to do this:

Функция taketree заменяет все узлы в дереве с узлами, которые имеют в
большинство n подузлов, используя функцию (take n), которая возвращает первые n элементов
из списка (или меньше если список короче чем n).
Еще одним усовершенствованием является уточнение отсечения. Программа выше проверяет
вперед на фиксированную глубину, даже если позиция очень динамична - она может принять решение
проверять не дальше чем позиция, в котором ферзь под угрозой, в шахматах например.
Обычно определяются такие четкие "динамические" позиции и не допускается предпроверка, чтобы не остановиться на одной из них. Предполагая что динамическая функция распознает
такие позиции нам нужно добавить только одно уравнение к prune чтобы сделать это:


prune 0 (Node pos sub)
= Node pos (map (prune 0) sub), if dynamic pos

Making such changes is easy in a program as modular as this one. As we
remarked above, since the program depends crucially for its efficiency on an
interaction between maximize, the last function in the chain, and gametree,
the first, in the absence of lazy evaluation it could be written only as a monolithic
program. Such a programs are hard to write, hard to modify, and very hard to
understand.

Легко делать эти изменения в таких же модульных программах как эта. Как мы
отмечали выше, как только эффективность программы в решающей степени зависит от
взаимодействия между maximize - последней функции в цепи, и gametree -
первой, в отсутствие ленивых вычислениий она могла бы быть написана только как монолитная
программа. Такие программы трудно писать, трудно модифицировать и очень трудно
понимать.
