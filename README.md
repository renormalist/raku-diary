This is a diary on learned lessons while learning Raku.

<!--ts-->
* [How to DWIM in a sea of types (2024-11-07)](#how-to-dwim-in-a-sea-of-types-2024-11-07)
   * [Unlike Perl, Raku is really a typed language](#unlike-perl-raku-is-really-a-typed-language)
   * [Operators to help you make the code DWIM](#operators-to-help-you-make-the-code-dwim)
   * [Pitfalls](#pitfalls)
   * [Life savers](#life-savers)
   * [Junctions](#junctions)

<!-- Created by https://github.com/ekalinin/github-markdown-toc -->
<!-- Added by: renormalist, at: Do 7. Nov 03:57:07 CET 2024 -->

<!--te-->

How to DWIM in a sea of types (2024-11-07)
==========================================

I'm coming from Perl to Raku and try to start by porting seemingly
easy Perl code.

My first lesson was:

Unlike Perl, Raku is really a typed language
--------------------------------------------

To program in Raku in a Perl DWIM way ("Do What I Mean") means to know
the types and operators which cover that magic that was built into
Perl natively.

This mostly affects the handling of numbers, strings, and strings that
look like numbers, and undefined values.

Beginners who want to start with a Perl'ish (seemingly) untyped style
need to know the operators and types that encapsulate the DWIM-work
you know from Perl.

The keyword is "allomorphs". My revelation came from those two links:

- https://rakujourney.wordpress.com/2024/04/18/allomorphs-unicode/
- https://github.com/rakudo/rakudo/blob/main/src/core.c/allomorphs.rakumod

Operators to help you make the code DWIM
----------------------------------------

- `<1 2 3>` ... like qw() but create allomorphs

- `cmp` ... compare type-aware, returns a type `Order` (values `Same`, `More`, `Less`) you can feed into smart match

- `~~` ... smart match

Pitfalls
--------

- `foo(1, 2, 3)` is not `foo([1, 2, 3])` is not `foo([<1 2 3>])`

- `<1 2 3>` is more than just quote words but creates allomorphs. You need it.

- To create "nothing" in lists, instead of Perl's () relying on list flattening,  you can return Empty

- undefined values warn loud when used because of missing type, so you often need to handle it explicitly

- `Nil` is not `Any`

Life savers
-----------

- `dd()` ... simple built-in Data::Dumper:

```
   $ raku -e 'dd(5 cmp "6")'
   Order::Less

   $ raku -e 'dd(5 cmp "6" ~~ Same|Less)'
   Bool::True
```

- `Data::Dump::Tree` ... fancy Data::Dumper

  - https://raku.land/github:nkh/Data::Dump::Tree


Junctions
---------

Did you see the `Same|Less` above?

That's a junction, i.e. multiple values in one place, here `Same` and
`Less` which all get processed implicitly without you doing stunts. In
the example the smartmatch

... `~~ Same|Less`

matches if left side matches `Same` or if it matches `Less`.
