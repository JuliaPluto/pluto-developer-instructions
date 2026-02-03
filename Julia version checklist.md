# How make Pluto compatible with the latest version of Julia?
It is really useful if Pluto is compatible with the latest version of Julia. Here are the steps to follow to get Pluto updated.



## Check Julia release notes
Carefully check each changed item and evaluate how it affects Pluto. 



## Tests
Run the Julia package tests for Pluto. If a tests starts to fail, make sure you fully understand the intention of the test, and the cause of the failure. 

Evaluate: Do we still want the test to pass in this Julia version? Or did Julia change in a way that Pluto should follow?


Also run the frontend tests (or check the CI logs).

## Dependencies
It is very important to make sure that Pluto's dependencies are compatible. You should check them all, but here are some things to pay attention to:

### ExpressionExplorer.jl
(Made by Pluto.) This package should not break too much. But when new features are added to Julia's syntax, ExpressionExplorer.jl needs to be updated. Check the Julia release notes. Write tests first (look at the other tests), and then work on an implementation.

### PlutoDependencyExplorer.jl
(Made by Pluto.) This package should not break too much.

### Malt.jl
(Made by Pluto.) This package is quite sensitive to Julia versions, because Julia changes implementation details often. This might take a lot of work.

Run the test suite, and see what fails. Particularly sensite are interrupt (broken in Julia 1.13, a complete new interrupt implementation is coming in a later release that needs to be made compatible). 

### GracefulPkg.jl
(Made by Pluto.) This package is very sensitive to Julia versions, but the work to make it compatible is usually small. Run the test suite, and see what fails. Look at the Pkg release notes and the test logs. Pkg usually gets nice improvements that you need to take into account, don't counteract improvements to Pkg.

### MsgPack.jl
As of 2026 this package is seemingly unmaintained(?), so you might need to get in touch with past maintainers, try to motivate new maintainers or start a fork.

### HTTP.jl
This package usually breaks with new Julia versions, so you need to wait for a compatible version. This usually happens during the Julia beta period.

### PrecompileSignatures, PrecompileTools
These packages (and the way we use them) are very much subject to change. The best strategy is to benchmark and evualuate the use of these packages with each Julia release.

### Other dependencies
The rest should be fairly stable!








## Version warning
When you are done, and the new Julia minor version has been released, it's time to update the version warning in Pluto. Pluto automaticlaly warns when using it with a Julia version from the future. Search for `warn_julia_compat`. Also create a new tracking issue like https://github.com/fonsp/Pluto.jl/issues/3389 and update `julia_compat_issue`.



## Release a new Pluto version
After everything is done, it's time to release a new Pluto version. Follow the release instructions.



# When to work on a new version?
We recommend to wait before working on Julia compatibility until the beta release or the final release of a new version. We find that Julia nightly often contains bugs that will be removed before the final release, and working on compatibility too early can waste a lot of time.

