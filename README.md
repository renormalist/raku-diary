This is a diary on learned lessons while learning Raku.

<!--ts-->
* [How to DWIM in a sea of types (2024-11-07)](#how-to-dwim-in-a-sea-of-types-2024-11-07)
   * [Unlike Perl, Raku is really a typed language](#unlike-perl-raku-is-really-a-typed-language)
   * [Operators to help you make the code DWIM](#operators-to-help-you-make-the-code-dwim)
   * [Pitfalls](#pitfalls)
   * [Life savers](#life-savers)
   * [Junctions](#junctions)
* [Bash completion for Raku tools (2024-11-02)](#bash-completion-for-raku-tools-2024-11-02)
* [Using and creating modules (2024-10-31)](#using-and-creating-modules-2024-10-31)
   * [Maintaining Raku distros with mi6](#maintaining-raku-distros-with-mi6)
   * [Creating a Raku module for zef](#creating-a-raku-module-for-zef)
   * [Zef just works(tm)](#zef-just-workstm)
   * [How to delete a module from zef](#how-to-delete-a-module-from-zef)
* [Entering the town of Raku (2024-04-28)](#entering-the-town-of-raku-2024-04-28)
   * [Variables and containers](#variables-and-containers)

<!-- Created by https://github.com/ekalinin/github-markdown-toc -->
<!-- Added by: renormalist, at: Fr 8. Nov 18:09:12 CET 2024 -->

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

- To create "nothing" in lists, instead of Perl's () relying on list
  flattening, you can return `Empty`

- undefined values warn loud when used because of missing type, so you
  often need to handle it explicitly

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


Bash completion for Raku tools (2024-11-02)
===========================================

Some Raku cmdline tools go smoother with TAB completion. Try

- https://github.com/renormalist/raku-tools-bash-completion


Using and creating modules (2024-10-31)
=======================================

Maintaining Raku distros with mi6
---------------------------------

You can use App::mi6 as found in

 - https://raku-intro.readthedocs.io/en/latest/modules.html#using-app-mi6

to maintain a Raku module distro.

Creating a Raku module for zef
------------------------------

Ok, I think I'm in the game.

- https://raku.land/zef:renormalist

Adding "zef:renormalist" manually to META6.json took me longer then
neccesary, learning to read would help. :)

So that's how you become a Raku module author:

    $ zef register
    $ mi6 new Foo::Bar
    # setup git(hub) repo origin
    # add "auth:AUTHORNAME" to META6.json
    # hack hack hack
    # edit Changes
    # git commit
    $ mi6 test
    $ mi6 release

Easy money.

Zef just works(tm)
------------------

The crazy thing is that I never had to fiddle with all the usual
modern obstacles, like registrations, confirmations, captchas, etc..

It just worked.

I spent most of my time trying to find what complicated process to
follow when in fact there was none.

The zef ecosystem is runnng on AWS S3 storage and lambdas. My inner
oldschool me needs to relax a bit from not really seeing what's going
on.


How to delete a module from zef
-------------------------------

The fez cleanup is on its way to stick within 24h:

    fez remove 'Data::DPath6:ver<0.0.1>:auth<zef:renormalist>'
    fez remove 'Data::DPath6:ver<0.0.2>:auth<zef:renormalist>'

Why the 24 hours? From what I read here:

- https://github.com/tony-o/raku-fez/blob/master/docs/Fez/API.md#remove
- https://github.com/tony-o/raku-fez/blob/master/lib/Fez/API.rakumod#L310

and

- https://deathbyperl6.com/faq-zef-ecosystem/
- https://deathbyperl6.com/fez-zef-a-raku-ecosystem-and-auth/

I *speculate* that the zef ecosystem is meant to store modules
eternally and the 24h is the grace period to recover from freakups
like contained sensitive data, so it maybe keeps the files in some
interstage?

More authoritative answers welcome.

Entering the town of Raku (2024-04-28)
======================================

Diane, it's April '24, weather is good, partly cloudy, 21°C, entering
the town of sigils formerly known as Perl6. I just installed the
latest Raku and put Camelia on a screenshot.

![Camelia on a screenshot which shows installing Raku](img/screenshot-with-rakubrew-and-camelia.png?raw=true "Camelia on a screenshot which shows installing Raku")


Variables and containers
------------------------

First steps are adapting to new wording of concepts I know. My
old-school Perl'sonality knows scalars, arrays, hashes, functions.

It's now

- $ - Mu - no type constraint
- @ - Positional - eg. Array
- % - Associative - eg. Hash
- & - Callable - eg. Subs

respectively all the derived variants.

See https://docs.raku.org/language/variables
