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

Полезно провести аналогию между функциональным и структурным программированием. В прошлом, характеристики и преимущества структурного программирования были более-менее определены в следующих чертах: такие программы не содержат операторов goto, программные блоки в них не имеют множественных входов и выходов и структурные программы более математически согласованы в отличие от их неструктурированных аналогов. Эти «преимущества» структурного программирования очень похожи по духу «преимуществам» функционального программирования, о котором мы упоминали ранее.  По сути, они являются негативными утверждениями и привели к большому количеству бесплодных агрументов типа « необходимые операторы goto» и т.д. Огладываясь назад, ясно что эти свойства структурированных программ хотя и полезны, но не раскрывают сути вопроса. Самое важное различие структурированных программ от неструктурированных является то, что структурированные программы разработаны по модульному принципу. Модульная структура программ сама по себе влияет на повышение производительности. Прежде всего, небольшие модули могут быть написаны быстро и легко.  Во-вторых, универсальные модули могут быть использованы повторно, что приводит к более быстрому развитию последующих программ. В-третьих, модули программы могут быть проверены независимо друг от друга, что помогает сократить время, затрачиваемое на отладку.

The absence of gotos, and so on, has very little to do with this. It helps with
“programming in the small”, whereas modular design helps with “programming
in the large”. Thus one can enjoy the benefits of structured programming in
Fortran or assembly language, even if it is a little more work.
It is now generally accepted that modular design is the key to successful
programming, and recent languages such as Modula-II [6] and Ada [5] include
features specifically designed to help improve modularity. 

Отсутствие goto и т.п. имеет мало общего с этим. Оно помогает при «программировании в малом», в то время как модульная структура помогает при «программировании в целом». Таким образом, можно пользоваться преимуществами структурного программирования на языке Фортран или ассемблере, даже если это приводит к небольшому увеличению работы. В настоящее время принято считать, что модульная конструкция является ключом к успешному программированию и такие языки  как Modula-II [6] и Ада [5] включают в себя функции, специально предназначенные для улучшения модульности.

However, there is a very important point that is often missed. When writing a modular program to
solve a problem, one first divides the problem into subproblems, then solves the
subproblems, and finally combines the solutions. The ways in which one can
divide up the original problem depend directly on the ways in which one can glue
solutions together. Therefore, to increase one’s ability to modularize a problem
conceptually, one must provide new kinds of glue in the programming language.
Complicated scope rules and provision for separate compilation help only with
clerical details — they can never make a great contribution to modularization.
We shall argue in the remainder of this paper that functional languages provide two new, very important kinds of glue. We shall give some examples of programs that can be modularized in new ways and can thereby be simplified.
This is the key to functional programming’s power — it allows improved modularization. It is also the goal for which functional programmers must strive — smaller and simpler and more general modules, glued together with the new
glues we shall describe.

Однако, есть очень важный момент, который часто упускается. Чтобы решить проблему при написании модульной программы неообходимо сначала разделить проблему на подзадачи, затем решить подзадачи и в конечном итоге скомбинировать решения подзадач. Способы, которыми можно разделить исходную проблему на подзадачи  непосредственно зависят от способов, которыми можно «склеить» решения подзадач вместе. Поэтому, чтобы концептуально увеличить свою способность модуляризировать проблему нужно обеспечить новые виды «клея» на языке программирования. Запутанные правила и обеспечение отдельной компиляции помогают только  в несущественных деталях, которые никогда не смогут внести большой вклад в модульность программы. В остальной части этой работы мы утверждаем, что функциональные языки программирования предоставляют два новых, очень важных видов «клея». Мы приведем некоторые примеры программ, которые могут быть модуляризированы новыми способами и соответственно упрощены. Это ключ к мощи функционального программирования, который позволяет улучшить модульность программ. Это также цель, к которой функциональные программисты должны стремиться - меньше и проще структура и более универсальные модули, склеенные новыми клеями, которые мы опишем.

