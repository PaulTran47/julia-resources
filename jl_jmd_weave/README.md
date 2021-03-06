


## Overview
With the current development of [Julia for VSCode](https://www.julia-vscode.org/), my default is to begin creating Julia scripts directly in a Julia Markdown (.jmd) file. This is because Julia for VSCode has [Weave](https://github.com/JunoLab/Weave.jl) integration, allowing it to compile and output Julia scripts in a pretty format.

This .jmd file serves as both a brief guide for setting up the Julia scripts + Julia Markdown + Weave workflow and very simple showcase of the workflow's results.

---

## Prerequisites
* Having [VSCode](https://code.visualstudio.com/) installed;
* Having the [Julia for VSCode](https://www.julia-vscode.org/) VSCode extension installed;
* Having [Weave](https://github.com/JunoLab/Weave.jl) installed using Julia's package manager, [Pkg](https://github.com/JuliaLang/Pkg.jl);
* Having some knowledge of Markdown syntax (though Google always exists).

---

## Useful documentation:
* [Weave documentation](http://weavejl.mpastell.com/stable/usage/);
* [Julia Markdown documentation](https://docs.julialang.org/en/v1/stdlib/Markdown/).

---

## Julia scripts + Julia Markdown + Weave workflow
#### Installing and using Weave
1. Open the VSCode command palette by typing "Ctrl/Cmd + Shift + P".
2. Type "julia" in the command palette to open Julia REPL (read, execute, print, loop).
3. Install Weave by typing "Pkg.add("Weave")" into Julia REPL.
4. Create your .jmd file, insert your desired Julia scripts, focus onto the Julia REPL, then type "weave("file.jmd")" (where "file" is your .jmd file name).
5. Open the output (.html file) and boom, your .jmd is now a beautiful file with Julia compiled scripts.
    * If you don't want to have your browser open all the time with the .html file, what you can do is type "weave" into the command palette, and choose the "Open preview to the side" option. This will render your .jmd output within VSCode.

#### Debugging
If a script requires complicated and real-time debugging, it is possible that temporary migration of the script into a Julia script (.jl) file is needed. **However**, this is likely not needed, as commands such as "@info" in the [Logging package](https://docs.julialang.org/en/v1/stdlib/Logging/) allow for output to be displayed in the REPL during the Weave process. As such, displaying diagnostics such as intermediate output from a loop in the REPL whilst working solely in a .jmd is possible.

---

## Compiled Julia scripts rendered by Weave
The following is an example of Julia script compiling within a .jmd file and rendered by Weave into a .html file:

```julia
# This is a regular comment.

#=
  This is a regular comment
  that is on more than one line.
=#

#= ANSWER
  This is an answer that doesn't involve code.
END ANSWER =#

#= NOTE
  This is an important note.
END NOTE =#

#= NOTE
 In Matlab, the functions I always run first are clear all; close all; clc.
 This is to clear out any old output and variables that are no longer
 desired. Julia's best analogy to these is to write scripts within modules.
 Then, each time the script is ran, the output made by the module (variables
 included) will be overwritten.
END NOTE =#

# Creating clc function
function clc()
  #= 
    Clearing console
  =#
  # For Windows
  Base.run(`cmd /c cls`)
  # For Unix
  # Base.run(`clear`)
end

# Calling clc function
clc()

# Declaring used packages
using Weave
using Logging

module lvls
  # Declaring levels
  U_dec82 = 11500000
  U_dec85 = 8100000
  E_dec82 = 99000000
  E_dec85 = 108200000
  L_dec82 = U_dec82 + E_dec82
  L_dec85 = U_dec85 + E_dec85
  N_dec82 = 62100000
  N_dec85 = 62800000
  P_dec82 = L_dec82 + N_dec82
  P_dec85 = L_dec85 + N_dec85
end

module part2a
  #=
    Part 2a: Calculate the unemployment rate in December 1982 and December 1985.
    u = U/L
  =#
  # Importing lvls module
  import ..lvls
  # Calculating unemployment rates
  u_dec82 = lvls.U_dec82 / lvls.L_dec82
  u_dec85 = lvls.U_dec85 / lvls.L_dec85

  @info "Printing to console: Unemployment rate in December 1982:" round(100*u_dec82; digits = 2)
  @info "Printing to console: Unemployment rate in December 1985:" round(100*u_dec85; digits = 2)
  println("Unemployment rate in December 1982: ", round(100*u_dec82; digits = 2))
  println("Unemployment rate in December 1985: ", round(100*u_dec85; digits = 2))
end

# Running garbage collector
GC.gc()
```

```
Unemployment rate in December 1982: 10.41
Unemployment rate in December 1985: 6.96
```





---

## LaTeX support in Weave
Julia Markdown also natively supports LaTeX, which is rendered as so:

```math
\begin{align}
  \Delta u_{t} &\approx \beta_{t - 1}(\Delta ln(s_{t}) - \Delta ln(f_{t})).
\end{align}
```

---

## Output
Weave allows multiple [output formats](http://weavejl.mpastell.com/stable/usage/#Supported-Output-Formats). My personal default are to create HTML and GitHub Markdown output (the latter so it can be displayed in my
[Github repository](https://github.com/PaulTran47/julia-resources)).

```julia
weave("jl_jmd_weave.jmd", doctype = "md2html")
weave("jl_jmd_weave.jmd", doctype = "github")
# Renaming GitHub Markdown output so it can be displayed properly on GitHub.
convert_doc("jl_jmd_weave.md", "README.md")
```



## Ending note
That's it. This workflow is that simple to get up and running. Information about useful Julia packages and bridges between Matlab and Julia functions can be found in other .jmd files I plan to create.

Kthxbaiii.

---