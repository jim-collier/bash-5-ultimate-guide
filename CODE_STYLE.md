<div align="center">

[![!#/bin/bash](https://img.shields.io/badge/-%23!%2Fbin%2Fbash-1f425f.svg?logo=gnu-bash)](https://www.gnu.org/software/bash/)
[![License: CC BY-NC-SA 4.0](https://img.shields.io/badge/License-CC%20BY--NC--SA%204.0-lightgrey.svg)](https://creativecommons.org/licenses/by-nc-sa/4.0/)
![Lifecycle: Beta](https://img.shields.io/badge/Lifecycle-Beta-yellow)
![Support](https://img.shields.io/badge/Support-Maintained-brightgreen)

</div>
<!--
[![License: CC BY-NC-SA 4.0](https://img.shields.io/badge/License-CC%20BY--NC--SA%204.0-lightgrey.svg)](https://creativecommons.org/licenses/by-nc-sa/4.0/)
![License: GPL v3](https://img.shields.io/badge/License-GPLv3-blue.svg)
![Lifecycle: Alpha](https://img.shields.io/badge/Lifecycle-Alpha-orange)
![Lifecycle: Beta](https://img.shields.io/badge/Lifecycle-Beta-yellow)
![Lifecycle: RC](https://img.shields.io/badge/Lifecycle-RC-blue)
![Lifecycle: Stable](https://img.shields.io/badge/Lifecycle-Stable-brightgreen)
![Lifecycle: Deprecated](https://img.shields.io/badge/Lifecycle-Deprecated-red)
![Status: Deprecated](https://img.shields.io/badge/Status-Deprecated-orange)
![Status: Archived](https://img.shields.io/badge/Status-Archived-lightgrey)
![Lifecycle: EOL](https://img.shields.io/badge/Lifecycle-EOL-lightgrey)
![Coverage](https://img.shields.io/badge/Coverage-25%25-red)
![Coverage](https://img.shields.io/badge/Coverage-50%25-orange)
![Coverage](https://img.shields.io/badge/Coverage-75%25-yellow)
![Coverage](https://img.shields.io/badge/Coverage-90%25-brightgreen)
![Status: Passing](https://img.shields.io/badge/Status-Passing-brightgreen)
![Status: Failing](https://img.shields.io/badge/Status-Failing-red)
-->

# Bash 5 coding style<!-- omit from toc -->

This project currently lives on [github.com/jim-collier/bash_v5_code-style](https://github.com/jim-collier/bash_v5_code-style).

## Table of contents<!-- omit from toc -->
- [Introduction](#introduction)
- [What about macOS and BSD](#what-about-macos-and-bsd)
- [Other style guides](#other-style-guides)
- [Tabs or spaces](#tabs-or-spaces)
- [Returning values from a function](#returning-values-from-a-function)
	- [Method 1: By *nameref* variable, function transforms input directly into output in-situ 😏](#method-1-by-nameref-variable-function-transforms-input-directly-into-output-in-situ-)
	- [Method 2: By *nameref* variable for output, but with separate read-only input 🤓](#method-2-by-nameref-variable-for-output-but-with-separate-read-only-input-)
	- [Method 3: By `echo` 🤔](#method-3-by-echo-)
	- [Method 4: Per-function global "return value" variables 😢](#method-4-per-function-global-return-value-variables-)
	- [Method 5: By literal variable *name*, `eval`, and *indirect parameter expansion* 😱](#method-5-by-literal-variable-name-eval-and-indirect-parameter-expansion-)
	- [Honorable mentions ✋](#honorable-mentions-)
- [Why `set -e` is so unforgivably bad, and why you might consider using it anyway \[WIP\]](#why-set--e-is-so-unforgivably-bad-and-why-you-might-consider-using-it-anyway-wip)
- [Copyright and license](#copyright-and-license)

## Introduction

Bash 5 coding standards also cover 4.3+, as the additional features of 5 don't involve coding style changed.

In most cases, there's no good reason to not code to 4.3+/5 features. There's too much pain involved with trying to write to < 4.3 or especially 3. (In that case you might as well just write POSIX-only Bourne Shell  scripts, for guaranteed maximum compatibility/portability.)

Version >= 4.3 has been standard in almost all Linux distributions since 2017.


## What about macOS and BSD

Version 3.2 (2007) is still the default on macOS at least up to Sequoia and probably beyond, due to GPLv3 licensing issues on Darwin. Darwin doesn't even include GNU coreutils (for the same reason), but the lesser-capable BSD variants. Anyone that is serious about scripting on Darwin, is going to rush to upgrade to Bash v5 and GNU coreutils, e.g. via `brew` - before they do anything else.

In most cases, we can't or shouldn't be held hostage by Darwin's ancient default tooling. (But if you are: again, consider just writing for Bourne Shell. [Or `sh` linked to `bash` 3.2 in POSIX mode, in the case of Darwin.])

As for BSD distros, they typically don't even include Bash by default, although its available. So again for maximum cross-platform compatibility you can target POSIX/Bourne (typically `sh` linked to `ash` or `pdksh` on BSD).


## Other style guides

Style guides are funny.

In many cases they have been honed over time as the result of hard lessons learned in the trenches by seasoned experts. Sometimes the reasons are so subtle and nuanced but ultimately profound and compelling, that the explanations of "why" would require a chapter in a textbook. And after reading a few such chapters, eventually you learn to just take some expert advice as articles of faith.

But in other cases, some "coding rules" are the result of superstition, entrenched misinformation or urban legend - or just a general unwillingness to question the Wisdom Of The Elders.

And it's often difficult to tell the difference. And in fact impossible, for any one person to fact check them *all*.

But conventions do change over time, even conventions once held as "sacred".

Does anyone remember [Hungarian Notation](https://en.wikipedia.org/wiki/Hungarian_notation)? While not exactly uncontroversial even at it's peak, it was incredibly useful at the time - when code editors were just dumb text editors, without robust tooling like type inference, coloring, in-place definition peeking, autocomplete, etc. Imagine a time when you couldn't even tell if something was a function, variable, or constant - just by color alone. The convention had weaknesses - it was cryptic for new programmers, and it made refactoring hard by unnecessarily coupling names and types. But the fact that the convention was used at all (e.g. by Microsoft) given its weaknesses, was a testament to how urgently useful the name-encoded information was to programmers.

So now we come to Google's ubiquitous [Shell Style Guide](https://google.github.io/styleguide/shellguide.html).

It was publicly released in 2016, and likely used internally much longer than that. While style guides are mostly a matter of opinion anyway - particularly on inherently memory-safe, loosey-goosey, untyped languages with a million ways to do the same thing - such as JavaScript or Bash - there is still a tremendous amount to criticize as "outdated thinking" or just plain silly, in Google's guide.

But even "silly" has to be taken in context. The parts this author finds the most "silly", comes with decades of Google-specific context and baggage.

For example: Google's "two space indentation" rule: Like, what the heck man, that's ridiculous. But we have to remember, this guide was only for their *own* internal consistency across innumerable code-bases, and going back to to their start in the 90s. In 1999, they were coding on - at best - 17" 4:3 CRTs with 1024x768 resolution.

There's no good reason modern non-Google programmers should observe such a rule. In fact, it could encourge (or at least fail to discourage) bad code practices such as over-nesting (which is covered in *this* guide.)

Some studies (e.g. [Bauer et al. 2019](https://www.researchgate.net/publication/335497893_Indentation_Simply_a_Matter_of_Style_or_Support_for_Program_Comprehension)) suggest that 4 spaces improve code readability, but arguably there just aren't yet any high-quality, conclusive studies on the matter.

Use two spaces if you want, but not because Google from 25 years ago says so for their own employees.

Here's an arguably better styleguide, from [CoddyRef](https://ref.coddy.tech/bash/bash-coding-style) (that also has other sections e.g. on performance tuning), but it still has many subjective faults. For example, it makes the thoroughly outdated and debunked plea to "use spaces instead of tabs for consistent formatting across different editors".

Tabs vs spaces literally doesn't matter by now in the second decade of the 21st century - and arguably never did, even for accessibility devices.

And even if you're concerned about tab width (e.g. 2 vs 4 vs 8 spaces) - every editor in existence allows adjusting the displayed width.

But more so than that, why even get in peoples' grills over how wide they personally prefer tabs to *display* on their own editors, as long as the use of tabs itself is consistent across a project?

(In fact allowing programmers that one nugget of harmless personalization that affects no one else across a shared codebase, is a checkmark in the "pro-tabs" column. If the argument against that is "well they might project their editor onto a screen in a meeting and *confuse everyone with wider or narrower tab spacing*". To which I suggest: If that confuses *you* specifically, you might consider a different career choice.)

People just seem to love arguing about tabs vs spaces and dying on that hill. Don't fall for it.

In fact, I'm not bothered by programmers using - gasp - *proportional fonts* - in their code editors. It's not for me, and it prevents the space-based lining up of same elements across adjacent lines. But A) editor plugins can help with that, and B) if they are productive, I don't care.


## Tabs or spaces

All that having been said, this style guide calls for:

**Tabs for indentation, and spaces for optional code-alignment after indentation**.

...*unless*...

- You've always done it the other way.
- Your project already uses spaces.
- Your organization uses spaces.
- You face political persecution or workplace harassment.
- You face political persecution or workplace harassment, specifically for using tabs.
- You hate the Tab and wish it was never born.
- You just prefer spaces over tabs.

<!--
## Naming convention [to-do]


## Code organization [to-do]


## Function structure [to-do]


## Indentation levels [to-do]


## Arguments [to-do]


## Constants [to-do]


## Variables [to-do]


## Comments [to-do]


## What to wrap in a function and what not to [to-do]
-->

## Returning values from a function

There is no "correct", "good", or even widely agreed-upon way to get values back from a Bash function.

There is only suffering.

All we can really do is try to balance inconvenience, against defiling the memory of our battle-scarred programming ancients.

Let's review the only reasonable - or some commonly-used - options.

*(Note: The examples here are necessarily munged and shortened for illustration - against many recommendations in this very document - for example without using atomicity protections. Function variables are also declared in these examples as `local` to make the scope clear, even though they'd still be local with `declare` in those contexts.)*


### Method 1: By *nameref* variable, function transforms input directly into output in-situ 😏

**Example**:
~~~
## Function
fRepeat(){
	local -n refVar=$1
	refVar="${refVar} ${refVar}!"
}

## Use
declare myVal="Mayday"
fRepeat myVal
echo "Repeated: '${myVal}'"
~~~

**Pros**:
- No risk of ruining the return value, with spurious output to `stdout` (as with the once-very common Method 3).
- Can even output to `stdout` on purpose, e.g. for long-running status messages.
- Debugging is easier.
- Can return multiple values to multiple variables at once this way.

**Cons**:
- It's obviously a dangerous habit for a "normal" programming language, for a function to deliberately overwrite the input with output - but none of this is "normal". Remember the balance we're trying to strike, noted above.
- More verbose for caller.
- Necessarily cannot protect caller's scope from the function's side-effects, by invoking in a subshell. (Which is usually not a deal-breaker for Bash scripts - there are already so many tradeoffs.)

**Use when**:
- The output is obviously the same "thing" as the input, and callers would probably just wind up overwriting their own input variable themselves with the function output anyway, with little or no validation. (E.g. low-stakes string cleanup.) This arguably covers most typical Bash function use-cases.


### Method 2: By *nameref* variable for output, but with separate read-only input 🤓

**Example**:

~~~
## Function
fRepeat(){
	local -n refVar=$1
	local -r inputVal="$2"
	refVar="${inputVal} ${inputVal}!"
}

## Use
declare myVal
fRepeat myVal "Mayday"
echo "Repeated: '${myVal}'"
~~~

**Pros**:
- Same as above, but also the function - if initialized properly - can't overwrite caller's *input* variable (and certainly won't intentionally by design).

**Cons**:
- Same as above.

**Use when**:
- The input and output are very different "things" (e.g. a filename input and date output).

### Method 3: By `echo` 🤔

**Example**:
~~~
## Function
fRepeat(){
	local -r inputVal="$1"
	echo "${inputVal} ${inputVal}!"
	echo "Debug" >&2
}

## Use
echo "Repeated: '$(fRepeat "Mayday")'"
~~~

*In this example, a local read-only variable was declared to hold the input, only to be consistent and directly comparable with the other examples. And the extra debug message, which doesn't exist in the other examples, is only to illustrate how to deal with not polluting the return value in this unique use-case. But in reality, since this option for returning values is mostly for the shortest functions, it's usually fine to just refer to "\$1" directly, and without any output to `stderr`. So the function could simply be:*
~~~
fRepeat(){ echo "${1} ${1}!"; }
~~~

**Pros**:
- The most readable option.
- The most concise option.
- Feels the most "natural" for regular programmers.

**Cons**:
- Can't output anything else to `stdout` in the function, which might not work for long-running functions that need to show status (and which by convention shouldn't go to `stderr`).
- Calling invokes a subshell. That's usually good to protect parent's scope from function's side-effects (and general separation of concerns), but not good if it's part of a collection of closely-related helper functions that share a parent function's state, to help organize code.
- You *must* remember to send errors and debugging to a logfile and/or stderr, so that the return value is not polluted.
- Even then, it's still easy to accidentally pollute the return value in longer functions calling multiple external programs you have little control over, including weird future edge cases you don't even know should be, if even *could* be, unit-tested.

**Use when**:
- A short function that returns a single value, and
- has minimal internal dependencies.

You should append "_ByEcho" (or something consistent) to the function name, to indicate the different way of dealing with this non-default function type.


### Method 4: Per-function global "return value" variables 😢

**Example**:
~~~
## Function
declare _fRepeat_RetVal=""
fRepeat(){
	local -r inputVal="$1"
	_fRepeat_RetVal="${inputVal} ${inputVal}!"
}

## Use
fRepeat "Mayday"
echo "Repeated: '${_fRepeat_RetVal}'"
~~~

**Pros**:

- This might have been a viable option long ago with very simple scripts.

**Cons**:
- Reentrant calls to the same function will clobber higher-nested return values.
- Adds clutter to the global namespace.
- Adds another unique class of bug, where new functions alter the global return value of an older existing function, between the time the intended function sets it, and returns control to the caller (e.g. by a sub-function that the function calls itself).
	- How would that even possible? Programmer mistake. Example 1: The "copy-paste then modify similar code" routine and forget to change that variable (we all do it even though its bad practice). Example 2: pragrammer-based autocorrect mistake.
	- Such bugs are subtle and can be tricky to track down, especially with lack of live debugging and automatic all-variable inspection.

**Use when**:
- Don't. (Not that global variables don't have perfectly valid uses. Just not in this narrow context.)


### Method 5: By literal variable *name*, `eval`, and *indirect parameter expansion* 😱

This method is almost identical in concept to Method 1, but works on Bash as old as v2 (1996).

However, this method requires the abuse of `eval` to work, which is bad, because:
- Makes things harder to debug.
- Can introduce impossible quoting conundrums.
- Unless you control *all* direct *and* indirect input to `eval` with absolute certainty, it introduces a potentially glaring (and potentially trivially-exploitable) security risk of arbitrary code-injection.

**Example**:
~~~
## Function
fRepeat(){
	local    refVar="$1"
	local -r refVar_Val="${!refVar}"
	eval "${refVar}=\"${refVar_Val} ${refVar_Val}!\""
}

## Use (this part is almost identical to Method 1, differing only by convention)
declare myVal="Mayday"
fRepeat "myVal"
echo "Repeated: '${myVal}'"
~~~

**Pros**
- Same as Method 1 above.

**Cons**
- Same as Method 1, and also:
- The additional problems and risks of using `eval`.

**When to use**
- Don't. This is just to illustrate the possibility.

There is also an `eval` version of Method 2, but you get the idea.


### Honorable mentions ✋

Arguably, none of these are better options than a contextually-appropriate choice among the first three, for normal script functions.

There may be specific approprite use-cases though, especially involving long-running cross-script IPC, where one of these might make more sense.

- Named pipes
	- Clumsy and verbose for "normal" functions.
	- Extra overhead to make sure pipes aren't already open, and to make sure they get closed.
	- Requires care to cleanup closed socket descriptors on the filesystem, including on error-handling.
	- Exposes data to other places on the system.
	- You could - and probably should for safety - abstract much of this work to one or more dedicated toolbox functions. But that would even further slow down long-running nested loops.

- File descriptor redirection. (E.g. above `&1` for `stdout` and `&2` for `stderr`. To be safe in Bash, these would be between `&10` and `&1024`.)
	- As clumsy and verbose as using named pipes for "normal" functions.
	- It would probably be a good idea to use unique descriptors per-function instance, so that functions calling each other don't clobber each others' data. However in Bash, by default, you only have 1,014 "safe" descriptors available per-process. This could get pretty hairy, especially for inherently reentrant functions.
	- Extra overhead to make sure descriptors aren't already open, and to make sure they get closed.
	- Two major benefits over named pipes:
		- Doesn't expose data to external locations on the system.
		- Doesn't leave lingering socket descriptors on the filesystem.
	- Same potential for handing off to toolbox functions - which further increases the web of complexity.

- UNIX domain sockets
	- Requires the use of an external program such as `socat`.
	- Might be exactly appropriate for complex IPC, across hosts, and/or involving multiple listeners - but total overkill for the traditional concept of a "function".

- Writing to and reading from a temp file, e.g. on memory-backed `/dev/shm` which almost all distros have, to cover in part such crude forms of IPC.
	- Although conceptually more crude, and still clumsy, it could be less visually cluttered than using named pipes or file descriptors. And not much slower.
	- If you forget to clean-up, the tmp files only persist until next reboot. (Though you could also create named-pipe descriptors on `/dev/shm` as well.)
	- As with named-pipes, it would probably be wise to hand off lifecycle management to a family of functions.
		- But more complicated, as each potentially simultaneously running nested call to each unique function in the script, in each potentially concurrently running instance of any script with the same toolbox functions - would need its own tmp file (as probably the fastest solution among more complicated).
			- At that point it would probably make sense for the caller to provide an on-the-spot generated GUID-based IPC tmp filespec, before calling every such function, so that every possible instance of every function across time and space, will have its own unique file to write to. From the caller's perspective, this might look something like:
				~~~
				ipc_Filespec="$(fIPC_GetFilespec)"
					## $ipc_Filespec might now look something like:
					## "/dev/shm/myscript.sh/UqrGtfE/RFx4ZWRQs03PQe7HA0Y0P.tmp"

				fRepeat "${$ipc_Filespec}" "Mayday"
				echo "Repeated: '$(ipc_EchoValAndRmFile "${$ipc_Filespec}")'"

				## Longer-running processes with repeated calls might just `cat` the file for results, and delete it later.

				## Be sure to add to err trap:
				{ [[ -n "" ]] && [[ -d "/dev/shm/myscript.sh/${ipc_ScriptInstance}" ]]; } && rm -rf "/dev/shm/myscript.sh/${ipc_ScriptInstance}"
				## Where the "${ipc_ScriptInstance}" subdirectory is created on first call to fIPC_GetFilespec().
				~~~


<!--
## Loops...


### ...with multiline string variables [to-do]


### ...with file contents [to-do]


## ...through filesytem structure [to-do]


## Arrays [to-do]


## Associative arrays [to-do]


## Error-handling [to-do]


## Minification [to-do]
-->

## Why `set -e` is so unforgivably bad, and why you might consider using it anyway [WIP]

[This](https://mywiki.wooledge.org/BashFAQ/105/Answers) article outlines excellent objective reasons why `set -e` is terribly flawed and inconsistent in Bash.


## Copyright and license

> © 2025 Jim Collier (bdYkHbGhCRb82V_TrJ0uLoPemb2EG5pbMNhBkYhb4BI=)<br>
> Licensed under the Creative Commons Attribution-NonCommercial-ShareAlike 4.0 International \([CC BY-NC-SA 4.0](https://creativecommons.org/licenses/by-nc-sa/4.0/)\)
