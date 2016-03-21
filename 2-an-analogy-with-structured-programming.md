# 2 An analogy with Structured Programming

It’s helpful to draw an analogy between functional and structured programming.
In the past, the characteristics and advantages of structured programming have
been summed up more or less as follows. Structured programs contain no goto
statements. Blocks in a structured program do not have multiple entries or exits.
Structured programs are more tractable mathematically than their unstructured
counterparts. These “advantages” of structured programming are very similar in
spirit to the “advantages” of functional programming we discussed earlier. They
are essentially negative statements, and have led to much fruitless argument
about “essential gotos” and so on.
With the benefit of hindsight, it’s clear that these properties of structured
programs, although helpful, do not go to the heart of the matter. The most important difference between structured and unstructured programs is that structured programs are designed in a modular way. Modular design brings with
it great productivity improvements. First of all, small modules can be coded
quickly and easily. Second, general-purpose modules can be reused, leading to
faster development of subsequent programs. Third, the modules of a program
can be tested independently, helping to reduce the time spent debugging.
The absence of gotos, and so on, has very little to do with this. It helps with
“programming in the small”, whereas modular design helps with “programming
in the large”. Thus one can enjoy the benefits of structured programming in
Fortran or assembly language, even if it is a little more work.
It is now generally accepted that modular design is the key to successful
programming, and recent languages such as Modula-II [6] and Ada [5] include
features specifically designed to help improve modularity. However, there is a
very important point that is often missed. When writing a modular program to
solve a problem, one first divides the problem into subproblems, then solves the
subproblems, and finally combines the solutions. The ways in which one can
divide up the original problem depend directly on the ways in which one can glue
solutions together. Therefore, to increase one’s ability to modularize a problem
conceptually, one must provide new kinds of glue in the programming language.
Complicated scope rules and provision for separate compilation help only with
clerical details — they can never make a great contribution to modularization.
We shall argue in the remainder of this paper that functional languages provide two new, very important kinds of glue. We shall give some examples of
programs that can be modularized in new ways and can thereby be simplified.
This is the key to functional programming’s power — it allows improved modularization. It is also the goal for which functional programmers must strive
— smaller and simpler and more general modules, glued together with the new
glues we shall describe.

