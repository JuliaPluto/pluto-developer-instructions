# How to create a Pluto.jl release

*Make sure that you are available to fix any problems during the next two hours.*

Here is how you release a new Pluto.jl version:
1. Run the new version of Pluto, and play around with a sample notebook to make sure that everything works.
    - **Important:** Open a couple of featured notebooks to check that they display correctly. *We do not have automatic tests to see if the featured notebooks work. If the structure of the statefile changed, this can cause them to fail without a CI failure.* You may need to follow [these instructions](https://github.com/JuliaPluto/pluto-developer-instructions/blob/main/How%20to%20update%20the%20featured%20notebooks.md) to update them.
3. *(Optional, but nice. Maybe we can automate this one day...)* The following sample notebooks have an embedded Manifest.toml: `Plots.jl`, `PlutoUI.jl` and `JavaScript`. Using Julia 1.6 (not 1.7), open these notebooks, and click the "update" button to update their packages. Commit these changes to the `main` branch. ([example](https://github.com/fonsp/Pluto.jl/commit/6b76953be6eb7ad805aa47d3b8ea1911ff6626ad)) *If these notebooks are very outdated, then users will have to download many packages twice: once to open the sample notebook, a second time if they use (newer versions of) those packages in a new notebook.*
4. Edit the `Project.toml` file to bump the version number.
5. Commit
6. Wait for tests to pass.
7. We have a [GH Action](https://github.com/fonsp/Pluto.jl/actions/workflows/Bundle.yml) that takes the code on the `main` branch, bundles the JS files for offline supports, and writes to the `release` branch. Go to the `release` branch, and wait for this commit to arrive. It should take 3 minutes. ([example](https://user-images.githubusercontent.com/6933510/150444129-53b664af-34c3-401f-9bd3-0f4dc8e30f19.png))
8. Find the commit that "GitHub Actions" just created on the `release` branch. **Wait for the test on that commit to pass.** These additional tests run the frontend tests on the bundle, with a disabled internet connection. This tests that the bundle is correct, and that we have offline support.
9. **Click** on that commit (on the `release` branch, authored by "GitHub Actions"), and comment `@JuliaRegistrator register()` in the commit comments ([example](https://github.com/fonsp/Pluto.jl/commit/6d956c1faa2bca2e4531d8df26b1716ae072869e#commitcomment-64277906)). Some notes:
    - *Triggering a release from the `release` branch instead of `main` means that we will have offline support!*
    - **Important:** from this point on, until the last step, there should be **no commits to `main`**. Committing to `main` would trigger the bundler to create a new `release` commit, overwriting the one that you are registering. This prevents the commit from being tagged later (see the `release` explainer at the bottom of this document), which means disaster. This is a flaw in our release process, fonsi will fix it after the summer. For safety, you could temporarily Disable the [Bundle workflow](https://github.com/fonsp/Pluto.jl/actions/workflows/Bundle.yml) at this point, and re-enable it after the release has been tagged.
10. **While waiting for the version to be merged into General**, you can still cancel it if you changed your mind: comment `Hold on!` on the General PR. You can also trigger `@JuliaRegistrator register()` on _another_ (newer) commit with the same version number, and this will replace the PR. Edit your previous comment to include `[noblock]`, and the clocks will start again.
11. Wait for the new version to be merged into General ([example](https://github.com/JuliaRegistries/General/pull/38455))
12. Wait for [TagBot](https://github.com/fonsp/Pluto.jl/actions/workflows/TagBot.yml) to tag a new release on fonsp/Pluto.jl ([example](https://github.com/fonsp/Pluto.jl/releases/tag/v0.14.8))
13. Wait for [this robot](https://github.com/fonsp/pluto-on-binder/actions/workflows/ReleaseLatest.yml) to update [pluto-on-binder](https://github.com/fonsp/pluto-on-binder) and tag a new version (but no release i think) ([example](https://github.com/fonsp/pluto-on-binder/tags))
14. Use the "Export to HTML" button inside Pluto to generate an export, and open it. This should work (i.e. the `jsdelivr` CDN should serve the new Pluto version)
15. Click the "Edit or Run this notebook" button, and click Binder. This will trigger a binder build.
16. If you know how, trigger binder builds for all mybinder.org providers.

If this is a **RECOMMENDED** release (i.e. we want all users to get a pop-up to update Pluto, we only do this for crucial bug fixes):
1. Wait at least 4 hours before marking a release as recommended, since there is a delay between the General registry and Julia's package servers.
3. Open the GitHub release notes and edit the description to contain `Recommended update` ([example](https://github.com/fonsp/Pluto.jl/releases/tag/v0.14.7))

If you have access to the plutojl.org account for cloudflare.com:
1. Go to Rules
10. Look for the Page Rule with a hard-coded Pluto version, and change it to the new latest version

### How does the `release` branch work?

The [bundler action](https://github.com/fonsp/Pluto.jl/actions/workflows/Bundle.yml) runs for every commit to `main`, it runs the bundler, generating the `frontend-dist` folder. This folder is normally gitignored, but the bunlder action will *force add*, *force commit*, and *force push* to the `release` branch. It also did a *hard reset to `main`*, which means that the `release` branch is always an exacty copy of the `main` branch, **plus one commit**, which adds the `frontend-dist` folder. 

This hard reset strategry means that old "bundle" commits disappear, and will eventually be garbage collected by GitHub. This avoids a very big `.git` folder. Now, this would be a problem: people won't be able to install released versions... But! Because we automatically [tag every released commit](https://github.com/fonsp/Pluto.jl/actions/workflows/TagBot.yml), all of the "bundle commits" (like https://github.com/fonsp/Pluto.jl/commit/3633cae1e9aa7c21db83857eaa397b15fd208a40 ) that were released are also tagged. A tagged commit is never garbage collected, even though they no longer belong to any branch.

