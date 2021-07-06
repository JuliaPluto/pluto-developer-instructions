# How to create a Pluto.jl release

Here is how you release a new Pluto.jl version:
1. Wait for tests to pass.
1. Run the new version of Pluto, and play around with a sample notebook to make sure that everything works.
2. Edit the `Project.toml` file to bump the version number.
3. Commit
4. Go to that commit (or whatever commit you want to release), and comment `@JuliaRegistrator register()` ([example](https://github.com/fonsp/Pluto.jl/commit/9482350b9101a7ece63919a40e453bf1912fd610))
5. Wait for the new version to be merged into General ([example](https://github.com/JuliaRegistries/General/pull/38455))
6. Wait for [TagBot](https://github.com/fonsp/Pluto.jl/actions/workflows/TagBot.yml) to tag a new release on fonsp/Pluto.jl ([example](https://github.com/fonsp/Pluto.jl/releases/tag/v0.14.8))
7. Wait for [this robot](https://github.com/fonsp/pluto-on-binder/actions/workflows/ReleaseLatest.yml) to update [pluto-on-binder](https://github.com/fonsp/pluto-on-binder) and tag a new version (but no release i think) ([example](https://github.com/fonsp/pluto-on-binder/tags))
8. Use the "Export to HTML" button inside Pluto to generate an export, and open it. This should work (i.e. the `jsdelivr` CDN should server the new Pluto version)
9. Click the "Edit or Run this notebook" button, and click Binder. This will trigger a binder build.
10. If you know how, trigger binder builds for all mybinder.org providers.

If this is a **RECOMMENDED** release (i.e. we want all users to get a pop-up to update Pluto):
1. Open the release and edit the description to contain `Recommended update` ([example](https://github.com/fonsp/Pluto.jl/releases/tag/v0.14.7))

If you have access to the plutojl.org account for cloudflare.com:
1. Go to Rules
10. Look for the Page Rule with a hard-coded Pluto version, and change it to the new latest version
