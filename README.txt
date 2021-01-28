Introduction to the sh- language

0: INTRO
========

The sh- language is a reduced version of shell scripting, inspired by the fish shell.
As you might notice, the name (sh-) alludes to this by being minus, i.e. less, of sh (shell).
It's called a language because there's no REPL to go along with it[1], and its syntax could
be repurposed as a more general language.

[1] For that matter, there's no interpreter to go along with it either - for now.

This document's examples will use a generic Linux distribution with standard GNU software.

1: BASIC SYNTAX
===============

In sh-, a program (script) is a list of statements.
These statements are seperated by newlines or semicolons (;).
When running a sh- program, these statements are executed sequentially, top to bottom.

A statement is a list of tokens seperated by whitespace (excluding linebreaks).
The leading token is called the "command", and the rest of the tokens are called "arguments".
When running as a shell script, a statement may be executed by invoking a corresponding
executable in the $PATH with the passed parameters being the arguments.

    echo Hello
    mv --help
    ls /

If one wishes to include whitespace in a token, the whitespace needs to be escaped.
This is done by prefixing it with a backslash. In sh-, the backslash is only used to escape reserved characters.

    echo Hello\ World

2: SUBSTITUTION
===============

Inside the list of tokens, you may use a pair of parentheses to open a substitution.
A substitution is simply another script. The output of this script is then used in the list of tokens
that make up the statement the substitution is used in.

    chown (id -un) /opt/sh-

A substitution must be a single token.
Unlike many shells, you cannot use this to do string interpolation, to prefix
arguments, or expand cartesian products.

    