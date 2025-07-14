# To-do

| Created     | Issue# |Pri| Started     | by  | Completed     | Description | Notes |
|-------------|--------|---|-------------|-----|---------------|-------------|-------|
| 20250713    |        | 3 |             |     |               | Naming convention
| 20250713    |        | 3 |             |     |               | Code organization
| 20250713    |        | 3 |             |     |               | Function structure
| 20250713    |        | 3 |             |     |               | What to wrap in a function and what not to
| 20250713    |        | 3 |             |     |               | Indentation levels
| 20250713    |        | 3 |             |     |               | Comments
| 20250713    |        | 3 |             |     |               | Loops with multiline string variables
| 20250713    |        | 3 |             |     |               | Loops with file contents
| 20250713    |        | 3 |             |     |               | Arrays
| 20250713    |        | 3 |             |     |               | Associative arrays
<!--
| 20250711    |        |   |             |     |               |
-->

## Random text for inclusion in document

Embracing native Bash v5 idioms (and not fighting against Bash in general), and a higher reliance on native idioms in general rather than wrapper functions in this template. Which allows for simplifying and removing functions.

However, a good example of absolutely necessary helper functions, are `fMath()` and `fRound()`. Rounding of floating-point numbers, for example - in a format that people are accustomed to seeing such as on a phone-app calculator (e.g. with leading and/or trailing 0s for floating-point, but otherwise with no insignificant 0s, etc.) - is *exceedingly* difficult to do in native Bash, even with the help of `mawk` and/or `bc`. The level of unit-testing and debugging was pretty insane, just to achieve 'normal' basic output.

On the other hand, typical string manipulations are by now trivially easy to do in native Bash. So relying too much on wrapper functions makes one a less effective bash developer over time. You might need to re-Google the exact terse, arcane syntax every time (e.g. for finding the index of a substring match), but it's still easy.

Or worst-case, bust out `sed -E`, `awk`, and/or `grep -P` in your script, to make short-work of any ginormous text-related problem, that can even make writing compiled code moot. (`fRound()` in this template, on the other hand, would absolutely choke possibly for hours on a loop reaching into the millions of iterations. You'd need a real language for that - but then you'd probably be mis-using a shell scripting language by then anyway.)

When to use native Bash, vs a wrapper or utility function:

- If a function can drastically reduce the amount of frequent boilerplate script I have to write (which can be considerable in Bash), I'll put it in a function. Or,

- If it's a more tricky multi-line problem to solve but needs to be done quite often and there's little risk of forgetting how to use the language...it goes in a function. And last but certainly not least,

- If it's a problem that needs solving often enough but is fraught with really subtle, hard-to-remember syntax gotchas that could hose the filesystem or sneakily corrupt working values - or is just a nightmare to debug every time - it goes in a script.
