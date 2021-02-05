# Introduction to the `sh-` language

NOTE: This language isn't recommended for professional or regular use.

## 0: INTRO

The `sh-` language is a reduced version of shell scripting, inspired by the fish shell.  
As you might notice, the name (`sh-`) alludes to this by being minus, i.e. less, of sh (shell).  
Unfortunately, this design goal may not have been achieved completely.[^0]  
It's called a language because there's no REPL to go along with it[^1], and its syntax could  
be repurposed as a more general language.

[^0] Some might say, not at all.  
[^1] For that matter, there's no interpreter to go along with it either - for now.

Throughout this document, we will use the builtin `#` command to make comments. Lines (statements) that start with it are effectively ignored.  
This document's examples will use a generic Linux distribution with standard GNU software.

## 1: BASIC SYNTAX

In `sh-`, a program (script) is a list of statements.  
These statements are seperated by newlines or semicolons (`;`).  
When running a `sh-` program, these statements are executed sequentially, top to bottom.

A statement is a list of tokens seperated by whitespace (excluding linebreaks).  
The leading token is called the "command", and the rest of the tokens are called "arguments".  
When running as a shell script, a statement may be executed by invoking a corresponding  
executable in the $PATH with the passed parameters being the arguments.

```  
echo Hello  
mv --help  
ls /  
```

If one wishes to include whitespace in a token, the whitespace needs to be escaped.  
This is done by prefixing it with a backslash. In `sh-`, the backslash is only used to escape reserved characters.

```  
echo Hello\ World  
```

## 2: SUBSTITUTION

Inside the list of tokens, you may use a pair of parentheses (`()`) to open a substitution.  
It is usually succeeded by a period (`.`) or dollar sign (`$`). More about this later.  
A substitution's content is simply another script. The output of this script is then  
used in the list of tokens that make up the statement the substitution is used in.

```  
chown (id -un). /opt/sh-  
```

A substitution must not be less than a single token.  
Unlike many shells, you cannot use this to do string interpolation, to prefix  
arguments, or expand cartesian products.

A substitution is split into multiple tokens, which is controlled by suffixing the closing paren with:

- A period (`.`) to not split at all.  
- A dollar sign (`$`) to split along newlines.  
- A hash sign (`#`) to split along whitespace.  
- An asterisk (`*`) to split into characters.  
- A series of positive integers (seperated by whitespace) enclosed in brackets (`[]`),
  which will split at these indices. You may use another substitution in here.  
- A series of strings (seperated by whitespace) enclosed in braces (`{}`),
  which will split at any occurence of these strings. You may use another substitution in here.  
- A series of regular expressions (seperated by whitespace) enclosed in angle brackets (`<>`).
  This works analogous to the braces.

```  
++ -s + -- (echo Hello\  
World,\ how\ are\ you?).  
```  
```  
++ -s + -- (echo Hello\  
World,\ how\ are\ you?)$  
```  
```  
++ -s (\\n) -- (echo Hello\  
World,\ how\ are\ you?)#  
```  
```  
++ -s (\\n) -- (echo Hello\  
World,\ how\ are\ you?)*  
```  
```  
++ -s (\\n) -- (echo Hello\  
World,\ how\ are\ you?)[1 3]  
```  
```  
++ -s (\\n) -- (echo Hello\  
World,\ how\ are\ you?){r e}  
```

*Note:* `++` *and* `\n` *are builtin commands*

You can read the output of this in the `./EXAMPLES/SPLAT.txt` file.

Additionally, you can suffix the substitution with an exclamation mark. This has no effect if the program is run normally.

## 3: SCRIPT BLOCKS

`sh-` also offers a feature very similar to substitutions, called script blocks.  
Script blocks are surrounded by braces (`{}`).  
Instead of executing the command and substituting the value,  
script blocks represent their contents, exactly.  
However, a program will still fail to compile/parse/validate if a script block's contents aren't valid.  
As such, script blocks mainly allow for encapsulation.

Please note that script blocks are not a seperate data type, they are strings, like everything.

For example:

```  
echo {echo a}  
# returns  
echo a  
```

```  
echo {echo a(}  
# fails  
```

```  
echo {(echo echo\ echo)#!}  
# returns  
echo echo  
```

```  
echo {(echo echo\ echo).!}  
# returns  
echo\ echo  
```  
*Note: "echo echo" is not a real command (probably)*

## 4: BUILTIN COMMANDS

Since these features aren't very expansive, `sh-` includes a large number of builtin commands.  
These commands always take precedence over ones found in the $PATH.  
If a `sh-` program is used in a non-shell-scripting context, these are the only commands available.

See the dedicated builtins documentation for more info.  
Note that this info hasn't been written yet.
