# Initial impressions of Julia coming from Python (and C++, JavaScript ...)
* [Call for Proposals](https://pretalx.com/juliacon2021/cfp) due March 23.*

## Surprises / Gotchas
* 1-based indexing?? in 2021? I thought this battle was over. Esp with C/Python FFI, it's a weird choice.
* installing packages is weird. open up the repl, type `]`, and then "add X", or import Pkg; Pkg.add("X")? 
* Manifest.toml is cool but a bit non-obvious to figure out how to update it.
* methods on objects vs methods that take objects. I get it, but less discoverable. e.g. `haskey(mydict, "a")` instead of `has_key`?
    * `methodswith(Dict)` is a nice workaround (but also isn't itself discoverable!)
    * `methodswith` doesn't return sorted results! You need `sort(methodswith(Dict), by=m->m.name)` or `methodswith(Dict, Base)` to exclude other mod. And this actually doesn't include `values` or `keys` unless you do `methodswith(Dict, Base; supertypes=true)` (thanks [discourse](https://discourse.julialang.org/t/can-methodswith-return-everything/8026)!), which is a PITA to type over and over again.
    * `sort(methodswith(Dict, supertypes=true), by=m->m.name)` is another solution ...
    * So I defined (in the REPL): `help = T -> sort(methodswith(T, supertypes=true), by=m->m.name)`, and then type `help(Dict)`
* `JULIA_NUM_THREADS` environment variable or `julia -t 8` -- but it is limited by the number of virtual cores you have, so silently fails.
* script with command line args was a bit tricky to get started
* naive code isn't necessarily faster than Python due to c implementations for performance critical stuff in many python packages.
* unicode symbols as operators (e.g. `union` and `\cup` for sets)
* community is on slac, a place that doesn't seem to have unlimited history / storage? esp as a newb, surprising that questions aren't on stackoverflow. But discourse is better.
* typed lists: c[1] on a `CartesianIndex[]` fails, `CartesianIndex{2}[]` is what I wanted 
* splat operator `...` (but this is [known](https://docs.julialang.org/en/v1/manual/faq/#What-does-the-...-operator-do?))
* use of semicolons also confusing. 
* `key in Dict` doesn't work
* creating a project is a bit confusing, especially if you want to "compile a binary" to save the 20 seconds of startup time...  PackageCompiler.jl seems like the way to go, but where do you learn about Project.toml, etc? Most of the documentation is about creating Modules, assuming that you are building a library that someone else will use. But what if you are building something that uses the library? Is that supposed to be a module too?

## Pros
* Friendly community  (e.g. [this thread](https://discourse.julialang.org/t/hierarchical-merge-on-a-region-adjacency-graph/51633))
    * PR here: https://github.com/JuliaImages/ImageSegmentation.jl/pull/64
* Helpful community [my-script-performance-lags-python](https://discourse.julialang.org/t/my-script-performance-lags-python/52249)
* everything is written in Julia, unlike in Python, where at some point you find yourself in CPython or ctypes or cython, or other very performant and very unreadable code. Or maybe you go after the GIL-less python, or try PyPy, Jython, IronPython ...).
* Gradual typing is cool, and the type system is way more powerful than python (generating more performant code!).
    * `@code_warntype`
* Writing threading code is a big win over python.
* There's a decent number of libraries
* language is evolving rapidly (e.g. see the [v1.6 release notes](https://github.com/JuliaLang/julia/blob/v1.6.0-beta1/NEWS.md))


## Cons
* Dict syntax isn't JSON (`Dict("a" => 1)`)
* script-based workflow is slow -- 30 seconds to run a test suite that fails immediately (typo on first line of test)
    * precompilation is tricky and involved, so it's hard to save the 10-20 seconds at the beginning of running your program with different input on the command line
* IJulia suffers compared to IPython, I think because readline support isn't as good (I use vim keybindings in IPython).
    * hacks like `jupyter console --ZMQTerminalInteractiveShell.editing_mode=vi --kernel=julia-1.5`
* There's no free lunch -- memory management (try not to allocate) is a thing for performance, and you can (and are encouraged to?) write code without exuberant allocs.
* There's no free lunch -- startup time is long for a script
* prevalence of `using X` flooding your namespace with a ton of functions. And the resulting conflicts (`WARNING: both RegionTrees and LightGraphs export "vertices"; uses of it in module Main must be qualified` <-- what does this even mean?). You can `import X` or even `import X: vertices` which seems like progress, but apparently that's not the Julia way?
    * resulting conflicts [will be able to be solved in the Julia 1.6](https://github.com/JuliaLang/julia/issues/1255) (see [this commit](https://github.com/JuliaLang/julia/commit/40fee86878068d5c7c833655b7637820fb2cba33)) with `import x: vertices as myvertices`
* there aren't as many full-featured libraries as there are in python (SLIC is not part of ImageSegmentation.jl ... yet?)
    * maybe this is a hidden pro: you get to write more code?
* [bugs in (presumably) commonly used frameworks](https://github.com/JuliaGraphs/SimpleWeightedGraphs.jl/issues/66#issuecomment-768448037) exist!

