[![Actions Status](https://github.com/lizmat/P5defined/workflows/test/badge.svg)](https://github.com/lizmat/P5defined/actions)

NAME
====

Raku port of Perl's defined() / undef() built-ins

SYNOPSIS
========

    use P5defined;

    my $foo = 42;
    given $foo {
        say defined();  # True
    }

    say defined($foo);  # True

    $foo = undef();
    undef($foo);

DESCRIPTION
===========

This module tries to mimic the behaviour of Perl's `defined` and `undef` built-ins as closely as possible in the Raku Programming Language.

ORIGINAL PERL 5 DOCUMENTATION
=============================

    defined EXPR
    defined Returns a Boolean value telling whether EXPR has a value other
            than the undefined value "undef". If EXPR is not present, $_ is
            checked.

            Many operations return "undef" to indicate failure, end of file,
            system error, uninitialized variable, and other exceptional
            conditions. This function allows you to distinguish "undef" from
            other values. (A simple Boolean test will not distinguish among
            "undef", zero, the empty string, and "0", which are all equally
            false.) Note that since "undef" is a valid scalar, its presence
            doesn't necessarily indicate an exceptional condition: "pop"
            returns "undef" when its argument is an empty array, or when the
            element to return happens to be "undef".

            You may also use "defined(&func)" to check whether subroutine
            &func has ever been defined. The return value is unaffected by any
            forward declarations of &func. A subroutine that is not defined
            may still be callable: its package may have an "AUTOLOAD" method
            that makes it spring into existence the first time that it is
            called; see perlsub.

            Use of "defined" on aggregates (hashes and arrays) is deprecated.
            It used to report whether memory for that aggregate had ever been
            allocated. This behavior may disappear in future versions of Perl.
            You should instead use a simple test for size:

                if (@an_array) { print "has array elements\n" }
                if (%a_hash)   { print "has hash members\n"   }

            When used on a hash element, it tells you whether the value is
            defined, not whether the key exists in the hash. Use "exists" for
            the latter purpose.

            Examples:

                print if defined $switch{D};
                print "$val\n" while defined($val = pop(@ary));
                die "Can't readlink $sym: $!"
                    unless defined($value = readlink $sym);
                sub foo { defined &$bar ? &$bar(@_) : die "No bar"; }
                $debugging = 0 unless defined $debugging;

            Note: Many folks tend to overuse "defined" and are then surprised
            to discover that the number 0 and "" (the zero-length string) are,
            in fact, defined values. For example, if you say

                "ab" =~ /a(.*)b/;

            The pattern match succeeds and $1 is defined, although it matched
            "nothing". It didn't really fail to match anything. Rather, it
            matched something that happened to be zero characters long. This
            is all very above-board and honest. When a function returns an
            undefined value, it's an admission that it couldn't give you an
            honest answer. So you should use "defined" only when questioning
            the integrity of what you're trying to do. At other times, a
            simple comparison to 0 or "" is what you want.

            See also "undef", "exists", "ref".

    undef EXPR
    undef   Undefines the value of EXPR, which must be an lvalue. Use only on
            a scalar value, an array (using "@"), a hash (using "%"), a
            subroutine (using "&"), or a typeglob (using "*"). Saying "undef
            $hash{$key}" will probably not do what you expect on most
            predefined variables or DBM list values, so don't do that; see
            "delete". Always returns the undefined value. You can omit the
            EXPR, in which case nothing is undefined, but you still get an
            undefined value that you could, for instance, return from a
            subroutine, assign to a variable, or pass as a parameter.
            Examples:

                undef $foo;
                undef $bar{'blurfl'};      # Compare to: delete $bar{'blurfl'};
                undef @ary;
                undef %hash;
                undef &mysub;
                undef *xyz;       # destroys $xyz, @xyz, %xyz, &xyz, etc.
                return (wantarray ? (undef, $errmsg) : undef) if $they_blew_it;
                select undef, undef, undef, 0.25;
                ($a, $b, undef, $c) = &foo;       # Ignore third value returned

            Note that this is a unary operator, not a list operator.

PORTING CAVEATS
===============

Parentheses
-----------

Because of some overzealous checks for Perl 5isms, it is necessary to put parentheses when using `undef` as a value. Since the 2018.09 Rakudo compiler release, it is possible to use the `isms` pragma:

    use isms <Perl5>;
    say undef;    # Nil

$_ no longer accessible from caller's scope
-------------------------------------------

In future language versions of Raku, it will become impossible to access the `$_` variable of the caller's scope, because it will not have been marked as a dynamic variable. So please consider changing:

    defined;

to either:

    defined($_);

or, using the subroutine as a method syntax, with the prefix `.` shortcut to use that scope's `$_` as the invocant:

    .&defined;

AUTHOR
======

Elizabeth Mattijsen <liz@raku.rocks>

Source can be located at: https://github.com/lizmat/P5defined . Comments and Pull Requests are welcome.

COPYRIGHT AND LICENSE
=====================

Copyright 2018, 2019, 2020, 2021 Elizabeth Mattijsen

Re-imagined from Perl as part of the CPAN Butterfly Plan.

This library is free software; you can redistribute it and/or modify it under the Artistic License 2.0.

