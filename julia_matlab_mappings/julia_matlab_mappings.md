---
author: "Paul Le Tran"
title: "Mappings between Julia and Matlab"
date: "05 November, 2021"
---


## Overview
This .jmd file serves as a single source and complilation of all useful mappings between Matlab and Julia functions from an economics perspective. Each section will be the name of the Matlab function(s). The immediate text below will be
describing the Matlab functions, then provide mappings to Julia.

---

## Prerequisites
* Having [VSCode](https://code.visualstudio.com/) installed;
* Having [Matlab](https://www.mathworks.com/products/matlab.html) installed;
    * Because Matlab is a propriety language behind a paywall, having [Octave](https://www.gnu.org/software/octave/index) installed is an free and open-source alternative with extremely similar syntax.
* Having the [Julia for VSCode](https://www.julia-vscode.org/) VSCode extension installed.
* Having [Weave](https://github.com/JunoLab/Weave.jl) installed using Julia's package manager, [Pkg](https://github.com/JuliaLang/Pkg.jl).

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

module showcase_nsolve
  # Declaring used packages
  using Weave
  using Logging
  using SymPy

  # Showcasing nsolve for 1 equation
  @vars x
  f = exp(x) - 2x^2
  soln_1 = nsolve(f, x, 3)
  @info "nsolve solution 1 is:" soln_1
  println("nsolve solution 1 is: ", soln_1)

  # Showcasing nsolve for system of equations (2)
  @vars x y
  eq1 = x + y -1
  eq2 = x - y - (-1)
  soln_2 = sympy.nsolve([eq1, eq2], (x,y), (1,1))
  @info "nsolve solution 2 is:" soln_2
  println("nsolve solution 2 is: ", soln_2)
end
```

```
nsolve solution 1 is: 2.617866613066812769178978059143202817320274359410482
919210508161040370325332
nsolve solution 2 is: SymPy.Sym[0; 1.00000000000000000000000000000000000000
0000000000000000000000000000000000000]
Main.##WeaveSandBox#259.showcase_nsolve
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

module showcase_nlsolve
  # Declaring used packages
  using Weave
  using Logging
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
  soln = nlsolve(f!, j!, [ 0.1; 1.2])
  @info "nlsolve solution is:" soln
  println("nlsolve solution is: ", soln)
end
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
Main.##WeaveSandBox#259.showcase_nlsolve
```





---