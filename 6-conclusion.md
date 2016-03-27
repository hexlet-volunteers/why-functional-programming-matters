# 6. Conclusion

In this paper, we’ve argued that modularity is the key to successful programming. Languages that aim to improve productivity must support modular programming well. But new scope rules and mechanisms for separate compilation
are not enough — modularity means more than modules. Our ability to decompose a problem into parts depends directly on our ability to glue solutions
together. To support modular programming, a language must provide good
glue. Functional programming languages provide two new kinds of glue —
higher-order functions and lazy evaluation. Using these glues one can modularize programs in new and useful ways, and we’ve shown several examples of this.
Smaller and more general modules can be reused more widely, easing subsequent
programming. This explains why functional programs are so much smaller and
easier to write than conventional ones. It also provides a target for functional
programmers to aim at. If any part of a program is messy or complicated, the
programmer should attempt to modularize it and to generalize the parts. He or
she should expect to use higher-order functions and lazy evaluation as the tools
for doing this.
Of course, we are not the first to point out the power and elegance of higherorder functions and lazy evaluation. For example, Turner shows how both can
be used to great advantage in a program for generating chemical structures [3].
Abelson and Sussman stress that streams (lazy lists) are a powerful tool for
structuring programs [1]. Henderson has used streams to structure functional
operating systems [2]. But perhaps we place more stress on functional programs’
modularity than previous authors.
This paper is also relevant to the present controversy over lazy evaluation.
Some believe that functional languages should be lazy; others believe they
should not. Some compromise and provide only lazy lists, with a special syntax
for constructing them (as, for example, in Scheme [1]). This paper provides
further evidence that lazy evaluation is too important to be relegated to secondclass citizenship. It is perhaps the most powerful glue functional programmers
possess. One should not obstruct access to such a vital tool.

