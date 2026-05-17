# To-do

| Created  | Issue# |Pri| Started  | by  | Completed | Description | Notes |
|----------|--------|-- |----------|-----|-----------|-------------|-------|
| 20250713 |        | 3 |          |     |           | Code organization
| 20250713 |        | 3 |          |     |           | ••• Multi-file, multi-developer
| 20250713 |        | 3 |          |     |           | ••• Compact style
| 20250713 |        | 3 |          |     |           | Naming convention
| 20250713 |        | 3 |          |     |           | Quoting
| 20250713 |        | 3 |          |     |           | Comments
| 20250713 |        | 3 |          |     |           | Error-handling
| 20250715 |        |   |          |     |           | ••• `set -eE; set -o pipefail; shopt -s inherit_errexit 2>/dev/null \|\| true`
| 20250715 |        |   |          |     |           | •••••• Not `set -u` because: 
| 20250713 |        | 3 |          |     |           | Functions
| 20250713 |        | 3 |          |     |           | ••• How to decide what to put in a function, vs leave in-line
| 20250713 |        | 3 |          |     |           | ••• The `function` keyword
| 20250713 |        | 3 |          |     |           | ••• Arguments
| 20250713 |        | 3 |          |     |           | •••••• Default values, required|optional, bool/integer validation, error-handling
| 20250713 |        | 3 |          |     |           | •••••• To option flags or not [not]
| 20250713 |        | 3 |          |     |           | •••••• Special consideration: *nameref* arguments
| 20250713 |        | 3 |          |     |           | ••• Minimize indentation levels by bailing early
| 20250715 |        | 3 |          |     |           | Performance
| 20250715 |        | 3 |          |     |           | ••• Add an indicator for each reco: relative speed boost, and sacrifice in capability for speed
| 20250715 |        | 3 |          |     |           | ••• Replace `sed` commands with Bash variable string manipulation.
| 20250715 |        | 3 |          |     |           | ••• Group adjacent `sed` commands into one.
| 20250715 |        | 3 |          |     |           | ••• Avoid unnecessary use of e.g. `true` and `:` (subprocces)
| 20250715 |        | 3 |          |     |           | Decision structures
| 20250715 |        | 3 |          |     |           | ••• Always use double brackets
| 20250715 |        | 3 |          |     |           | ••• Safe ternary operators with braces
| 20250713 |        | 3 |          |     |           | Loops
| 20250713 |        | 3 |          |     |           | ••• with multiline string variables
| 20250713 |        | 3 |          |     |           | ••• with file contents
| 20250713 |        | 3 |          |     |           | Arrays
| 20250713 |        | 3 |          |     |           | Associative arrays
| 20250715 |        |   |          |     |           | Error-handling
<!--                                                 |
| 2025MMDD |        |   |          |     |           |
-->

## Random text for inclusion in document

- You have to take extreme care to avoid variable name collisions when calling nested functions that all take *nameref* variables at the same argument position, which may be common. This is a glaring weakness of *nameref* variables, for all its strengths.
	- The easiest way to to this is to append a date/time-based encoded value to the end of any `local -n` variable declaration (as with the example above). While this may be reliable on a single machine, the more people are actively working on a project, the less so it becomes.
		- Forunately in Bash, it's not very multideveloper friendly - or at least, that rarely happens.
		- But if so, st some point you may either neeed millisecond precision, base-62 UUIDs, or some central repository of auto-incrementing, never-repeating values.

Embracing native Bash v5 idioms (and not fighting against Bash in general), and a higher reliance on native idioms in general rather than wrapper functions in this template. Which allows for simplifying and removing functions.

However, a good example of necessary helper functions, might be something like `fMath()` and `fRound()`, if you deal with math or rounding much. Rounding of floating-point numbers, for example - in a format that people are accustomed to seeing such as on a phone-app calculator (e.g. with leading and/or trailing 0s for floating-point, but otherwise with no insignificant 0s, etc.) - is *exceedingly* difficult to do in native Bash, even with the help of `awk` and/or `bc`.

On the other hand, typical string manipulations are by now trivially easy to do in native Bash. So relying too much on wrapper functions makes one a less effective bash developer over time. You might need to re-Google the exact terse, arcane syntax every time (e.g. for finding the index of a substring match), but it's still easy.

But for huge text-processing challenges - even ones that span large filesystems, short Bash scripts with `find -exec`, `sed -E`, `awk`, and/or `grep -P` can make short-work of large amounts of data that could even make writing compiled code moot. But if you put too much of that within in a Bash loop, then the suffering may start to commence.

When to use native Bash, vs a wrapper or utility function:

- If a function can drastically reduce the amount of frequent boilerplate script to write (which can be considerable in Bash).

- If it's a more tricky multi-line problem to solve but needs to be done quite often and there's little risk of forgetting how to use the language.

- If it's a problem that needs solving often enough but is fraught with subtle, hard-to-remember syntax gotchas that could hose the filesystem or sneakily corrupt working values - or is just a nightmare to debug every time.
