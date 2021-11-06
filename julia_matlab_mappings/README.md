


## Overview
This .jmd file serves as a single source and complilation of all useful mappings between Matlab and Julia functions from an economics perspective. Each section will be the name of the Matlab function(s). The immediate text below will be describing the Matlab functions, then provide mappings to Julia.

---

## Prerequisites
* Having [VSCode](https://code.visualstudio.com/) installed;
* Having [Matlab](https://www.mathworks.com/products/matlab.html) installed;
    * Because Matlab is a proprietary language behind a paywall, having [Octave](https://www.gnu.org/software/octave/index) installed is an free and open-source alternative with extremely similar syntax.
* Having the [Julia for VSCode](https://www.julia-vscode.org/) VSCode extension installed.
* Having [Weave](https://github.com/JunoLab/Weave.jl) installed using Julia's package manager, [Pkg](https://github.com/JuliaLang/Pkg.jl).

---

## Differences between Julia and Matlab
Before understanding mappings between the two languages, it's helpful to know the noteworthy differences first. The Julia documentation has a perfect summary for some
[noteworthy differences](https://docs.julialang.org/en/v1/manual/noteworthy-differences/#Noteworthy-differences-from-MATLAB) between the languages.

---

## [matlab-to-julia](https://lakras.github.io/matlab-to-julia/) translator
Because the syntax between the two languages are quite similar, there isn't much uprooting that is needed to begin moving to Julia. The linked translator does a lot of the basic work of converting Matlab code into Julia scripts.

NOTE: It should be mentioned that replacement of Matlab functions with Julia equivalents and proper usage of Julia functions and modules need to be done *manually* (e.g., Matlab's "clear all; close all; clc"). This translator is still immensely useful because majority of your Matlab code is able to be correctly converted.

---

## [clear all](https://www.mathworks.com/help/matlab/ref/clear.html); [close all](https://www.mathworks.com/help/matlab/ref/close.html); [clc](https://www.mathworks.com/help/matlab/ref/clc.html)
clear all; close all; clc is common Matlab practice of removing old workspace variables and output before a new project is started.

A combination of Julia coding style, functions, and VSCode functionality can achieve these Matlab functions:

* **clc**: Create a function that runs the the appropriate shell command for either Unix or Windows:
```julia
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
```

```
Process(`cmd /c cls`, ProcessExited(0))
```





* **clear all (Module method)**: Write Julia scripts within modules.
    * This results in all variables inside the module to be cleared upon each the .jl/.jmd file in the REPL. This coding style also clears functions too!

* **clear all; clc (VSCode command palette method)**: Stopping ("Alt + J", "Alt + K" in VSCode command palette) and restarting ("Alt + J", "Alt + O" in VSCode command palette) the Julia REPL. This will clear the entire Julia workspace.

* **close all**: VSCode command palette has the option "Julia: Delete All Plots" ("Shift + Delete" in VSCode command palette).

**NOTE**: Unlike Matlab, once a variable is declared in the main workspace, it stays there forever (until the REPL is closed, at least). Julia's excellent memory management makes this not an issue. As as a result,it's common practice to just overwrite variables if used more than once in a script not using modules, or within the same module. If memory usage becomes a concern within a .jmd/.jl file, you can simply set the variable(s) equal to "nothing". The memory will be released the next time garbage collection runs (which can be forced by running the command "GC.gc()").

### Best practice to emulate Matlab's clear all; close all; clc
1. Include clc() function at the top of every .jl/.jmd file.

2. Excluding variables or packages that are expected to be used in the entire programme, put all Julia scripts within modules.
    * Alternative is to write Julia functions instead of scripts.

3. Using VSCode's command palette, stop and restart Julia's REPL and delete all plots before every rerun of the .jl/.jmd file.

4. Set variables no longer needed to be seen or used to "nothing" for garbage collection.

5. Tell computer to run garage collector at the end of every .jl/.jmd file.

---

## [vpasolve](https://www.mathworks.com/help/symbolic/sym.vpasolve.html), [solve](https://www.mathworks.com/help/symbolic/solve.html)
*vpasolve* is a Matlab function that solves equations numerically; *solve* is the analytical equivalent. Both can be applied to linear and nonlinear system of equations as well.

### Julia packages
Julia offers several packages that provide equation system solvers. The following found are:
* [SymPy](https://github.com/JuliaPy/SymPy.jl);
* [NLsolve](https://github.com/JuliaNLSolvers/NLsolve.jl)

#### SymPy
SymPy provides several functions to solve equations (singular or system): *factor*, *roots*, *real_roots*, *solve*, *nsolve*. *nsolve* will be showcased here:

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

# Declaring commonly used packages
using Weave
using Logging

module showcase_nsolve
  # Declaring used packages within module
  using SymPy

  # Showcasing nsolve for 1 equation
  @vars x
  f = exp(x) - 2x^2
  soln_1 = nsolve(f, x, 3)
  @info "nsolve solution 1 is:" soln_1
  println("nsolve solution 1 is: ", soln_1)
  f = x = soln_1 = nothing

  # Showcasing nsolve for system of equations (2)
  @vars x y
  eq1 = x + y -1
  eq2 = x - y - (-1)
  soln_2 = sympy.nsolve([eq1, eq2], (x,y), (1,1))
  @info "nsolve solution 2 is:" soln_2
  println("nsolve solution 2 is: ", soln_2)
  eq1 = eq2 = x = y = soln_2 = nothing
end

# Running garbage collector
GC.gc()
```

```
nsolve solution 1 is: 2.61786661306681276917897805914320281732027435941048
2919210508161040370325332
nsolve solution 2 is: SymPy.Sym[0; 1.00000000000000000000000000000000000000
0000000000000000000000000000000000000]
```





#### NLsolve
Part of the [JuliaNLSolvers](https://github.com/JuliaNLSolvers) family that specialises in solving nonlinear system of equations. 

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

# Declaring commonly used packages
using Weave
using Logging

module showcase_nlsolve
  # Declaring used packages within module
  using NLsolve

  #=
    The following bivariate function of two variables is being solved:
    (x, y) -> ((x+3)*(y^3-7)+18, sin(y*exp(x)-1))
  =#

  # Declaring bivariate function
  function f!(F, x)
    F[1] = (x[1]+3)*(x[2]^3-7)+18
    F[2] = sin(x[2]*exp(x[1])-1)
  end

  # Declaring jacobian
  function j!(J, x)
    J[1, 1] = x[2]^3-7
    J[1, 2] = 3*x[2]^2*(x[1]+3)
    u = exp(x[1])*cos(x[2]*exp(x[1])-1)
    J[2, 1] = x[2]*u
    J[2, 2] = u
  end

  # Showcasing nlsolve
  soln = nlsolve(f!, j!, [0.1; 1.2])
  @info "nlsolve solution is:" soln
  println("nlsolve solution is: ", soln)
  soln = nothing
end

# Running garbage collector
GC.gc()
```

```
nlsolve solution is: Results of Nonlinear Solver Algorithm
 * Algorithm: Trust-region with dogleg and autoscaling
 * Starting Point: [0.1, 1.2]
 * Zero: [-3.7818049096324184e-16, 1.0000000000000002]
 * Inf-norm of residuals: 0.000000
 * Iterations: 4
 * Convergence: true
   * |x - x'| < 0.0e+00: false
   * |f(x)| < 1.0e-08: true
 * Function Calls (f): 5
 * Jacobian Calls (df/dx): 5
```





##### Additional resources
*SymPy* and *NLsolve* are essentially symbolic math Julia packages. The [following documentation](https://mth229.github.io/symbolic.html) has more useful information regarding symbolic expressions. It also provides a lot more information on solving equations with topics covering limits, differentiation, optimisation, and integration.

---