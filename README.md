<!-- 
 !-- README.md ---
 !-- 
 !-- Copyright (C) 2014 Brandon Invergo <brandon@invergo.net>
 !-- 
 !-- Author: Brandon Invergo <brandon@invergo.net>
 !-- 
 !-- This program is free software; you can redistribute it and/or
 !-- modify it under the terms of the GNU General Public License
 !-- as published by the Free Software Foundation; either version 3
 !-- of the License, or (at your option) any later version.
 !-- 
 !-- This program is distributed in the hope that it will be useful,
 !-- but WITHOUT ANY WARRANTY; without even the implied warranty of
 !-- MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the
 !-- GNU General Public License for more details.
 !-- 
 !-- You should have received a copy of the GNU General Public License
 !-- along with this program. If not, see <http://www.gnu.org/licenses/>.
  -->

# A minimalish-but-handy python start-up script

Do you enjoy some of the basic features of the alternative Python
interpreters out there but you find them to be otherwise bloated?  It
appears to be an under-appreciated fact that you can run a script
automatically when you launch the Python interpreter.  Using this
feature, I've found that I can implement a few basic features in the
standard Python interpreter that greatly improve its usage (for me).

Many of its capabilities should come as no surprise to people who are
familiar with Python: tab-completion, persistent history, etc.  Others
are non-standard and required a bit of hacking to get right:
syntax-highlighted completion results, auto-indentation, and printable
functions. 

To use this script, just set the `PYTHONSTARTUP` environment variable
to point to it, e.g. if the file is in `$HOME/.config/python/` put
this in your `.bashrc`:

    export PYTHONSTARTUP=$HOME/.config/python/python-startup.py

The script is compatible with both Python 2 and Python 3.

I highly recommend reading the code and modifying it to suit your
tastes.  Of course, if you hack up some cool new feature, I'd love to
hear about it [[1]](#fn1).

# Features

## Pre-loaded modules

This comes as a bit of a side-effect: in order to implement some of
the functions in the start-up script, some modules had to be imported.
The benefit of this is that you won't need to import some modules in
your interactive sessions,
particularly `os.path` and `sys`.

Also, for Python 2, the `print` function is imported from
`__future__`.  Additionally, the `pprint` function is automatically
imported.  `pprint` pretty-prints objects, making it much easier to
view large lists or dicts, for example.

## Persistent history

Your command history is saved between sessions, so you can recall
commands that you previously entered.  Note that your Python 2 and
Python 3 histories are stored separately (though this is easy enough
to change if you don't want that).  By default, the command histories
are saved in `$HOME/.local/share/python`.

## Colorized, history-aware prompts

Rather than the traditional `>>>` (standard) and `...` (continuation)
prompts, this script provides prompts that show the current line in
the command history file.  They are also colorized: the standard
prompt is green while the continuation prompt is yellow.  These colors
are easily customized in the script.

Before:

    >>> def foo(bar):
    ...     print(bar)

After (you'll have to imagine the colors in your mind):

    [26]> def foo(bar):
    (26)>     print(bar)

## Highlighted tab-completion of commands

When you are entering commands, you can hit the `<Tab>` key to
complete a command.

That's not terribly new and surprising.  However, this script also
highlights the completion results according to their syntactic
function: modules in yellow, functions in blue, etc.  It's
surprisingly useful.

## Indentation keyboard shortcuts

Since indentation is so important in Python but since we are occupying
`<Tab>` with completion, I added some handy shortcuts to handle
indentation for you.  `Ctrl-j` will indent four spaces while `Ctrl-u`
will unindent four spaces.  These commands behave the way you would
expect: you can use them with the cursor residing anywhere in the
line, even in the middle of text (as opposed to having to move to the
beginning of the line and entering the spaces manually) [[2]](#fn2).

## Context-specific auto-indentation

To save you some keystrokes, your commands will be automatically
indented as necessary.  For example, if you enter an `if` command,
then the next line will automatically indent itself one level
inwards.  If you then unindent a level, all lines after that will be
at the new indentation level. Note that the indentation will happen as
soon as you start typing, not as soon as the prompt appears like you
might expect [[3]](#fn3).

## Printable functions

This one's a bit non-standard, so it bears some explaining.  I found
that sometimes in my interactive sessions, I had forgotten the
specific details of a function definition.  However, going back
through the command history would only show it one line at a time, and
in reverse at that.  So, the start-up script provides a function
decorator, `saved_function` that allows you to recall and print the
definition of a function that was entered at the command-line
[[4]](#fn4).  Simply pass it the line in the command history where the
function definition will begin (i.e. the next line after the one where
the decorator is being entered).

For example:

    [27]> @saved_function(28)
    (27)> def foo(bar):
    (27)>     print(bar)
    [30]> # ...other stuff...
    # later... more work...hey, what does function foo do again?
    [48]> print(foo)
    def foo(bar):
        print(bar)

# Footnotes: 

<a name="fn1">
[1]  But I explicitly do not want to turn this into another iPython.

<a name="fn2">
[2]  For some reason, the Python `readline` module is quite inflexible
in its key-bindings.  You cannot override some existing bindings for
some reason.  For example, `Ctrl-i`, which would be a natural choice
for adding to the indentation level, is inextricably tied to the
`<Tab>` key, causing it to always do completion, even if you try to
re-bind it.

<a name="fn3">
[3] This is due to the nature of the Python `readline` module and, to
my knowledge, there's no way around it.  Another side effect of this
is that you will have to hit `<Enter>` a couple times to end a block
of code.  Imagine you enter a one-line function.  The function
definition is at indentation level 0 and the line that comprises the
body of the function will be at indentation level 1.  After you enter
the body line, if you start typing again, the text will be entered at
level 1.  However, if you just hit `<Enter>` to finish the block, the
`readline` module will accept that as "typing" and will first indent
to level 1 before entering the line.  The next time you hit `<Enter>`,
the previous blank line will be recognized by the auto-indentation
code and the block will be completed.  Again, to my knowledge, there
is no way around this behavior (but I welcome any bug-fixes for it).

<a name="fn4">
[4]  If you want to see the definition of an imported function, you can
use the `getsource` function of the `inspect` module.
