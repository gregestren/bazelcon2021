# BazelCon 2021 Q&A Notes

#### Q (cross-compilation): How can we set target platform build flags that don't also apply to the host platform?
A ([@katre](https://github.com/katre)): This is a recognized deficiency in the Bazel API. We need more collaborative brainstorming to understand exact needs and turn them into a better API.

#### Q: (Starlarkification) What are plans for code coverage? Particularly C++? Would Starlarkifying the C++ rules help?
A ([@comius](https://github.com/comius)): We don't have obvious solutions now, especially since it varies by language. C++ Starlarkification is ongoing: expect more visibility in Github soon.

#### Q: (release process) How's the long-term-support (LTS) / rolling release policy going?
A ([@philwo](https://github.com/philwo)): Bazel 4.0 was the first LTS. Works well from core dev perspective. But we welcome community feedback. 5.0 is the next LTS release, which is on schedule. 

#### Q: (release process) Any plans to backport 5.0 fixes to 4.x branches?
A ([@philwo](https://github.com/philwo)): LTS policy is to restrict backports to critical fixes. We can be flexible but we're cautious about weakening this policy. Can always consider case-by-case needs.

#### Q: (Windows / remote execution) Running Bazel on Windows with a Linux execution platform works poorly. Any suggestions?
A ([@meteorcloudy](https://github.com/meteorcloudy)): This combination is hard to make work since build tools refer to the host platform. Currently remote execution requires the host and execution platform to be the same. We can visit design changes to improve this.

#### Q: (Apple) Bazel supports Apple Silicon but some community rule sets don't. What's the broader Apple Silicon plan? Can you provide a clear tutorial for it?
A ([@philwo](https://github.com/philwo)): CI support for Silicon is still primitive. We should wait for the broader ecosystem to move to Silicon, but that should happen over time since the use case is compelling.

Core Bazel should "just work" now. We're not aware of specific problems: please file bugs to inform us.

Some checked in binaries (like Java tools) might not be ready yet. As we add more machines to Bazel CI we should catch these issues more reliably.

For tutorials, community contributions are welcome.

#### Q: (Java) How's support for latest Java versions going? Java 17 support would be fantastic.
A ([@jhfield](https://github.com/jhfield)): We could consider more formal policy windows for version support. Current answer is "as soon as we can get to it" but we're hearing the feedback you may want something more reliable.

#### Q: Which part of Bazel would you re-design if re-starting from scratch?
A ([@meteorcloudy](https://github.com/meteorcloudy), [@wyverald](https://github.com/wyverald)): Multi-repository (external dependency) support. We're actively revamping this though: see bzlmod (including presentations at BazelCon).

#### Q: (Starlark) Any plans for the Starlark rule testing story?
A ([@brandjon](https://github.com/brandjon)): We have an internal rules committee at Google asking for this. Nothing concrete to report yet.

#### Q: (platforms / toolchains) Where are platforms heading?  Is there a platform extension API coming?
A ([@katre](https://github.com/katre)): For practical platform definitions, we're cautious not to expand @bazelbuild/platforms too eagerly: want to really restrict it to truly ubiquitous platforms. 

For the broader platforms API, we're actively working now to standardize —platforms across Bazel. A lot of this work is invisible since it requires migrating rule sets and consuming projects with various owners. When we've landed that, that'll be a good time to consider API expansions like platform extensions.

#### Q: (C++) Will Starlarkification port include scanning / .d pruning? If not, how will you solve this problem? impl_deps would require all external repositories to support impl_deps and explicit cc_library declarations.
A ([@lberki](https://github.com/lberki)): We reluctantly support include scanning: it's design-awkward but has practical benefits. We are offering support through an –experimental flag. We're trying to avoid Starlark support since we don't want to embed language-specific logic into Starlark.

#### Q: (Android) Any updates on rules_android Starlark migration?
A ([@ahumesky](https://github.com/ahumesky)): We're in the progress of moving Android rules to Starlark. There's a branch in android_rules that shows this. We won't support every Android feature immediately (like data binding). Android rules uses a slightly old version of Android tools (v. 25). It's tricky to update but we can do backporting to support new features sooner.

Which features won't be available immediately?
android_instrumentation_test, roboelectrict_test will remain native for a while. But we'll continue to support them as native implementations.

#### Q: (platforms / toolchains) What's the status on migrating native rules to toolchain resolution, particularly C++?
A ([@katre](https://github.com/katre)): C++ should fully work (if you flip an incompatible flag). We haven't flipped the flag yet because depending Android / iOS projects break until they support toolchains. This is why we're focusing on migrating Android / IOS now.

#### Q: (platforms / toolchains) How do we maintain toolchains for non-standard platforms? If I write my own toolchain will I have to update it often due to API changes across Bazel releases? How stable are the core APIs?
A ([@katre](https://github.com/katre)): You should write your own toolchains for custom platforms. We intend to make the API reliably stable, and communicate breaking changes carefully. Core Bazel isn't trying to explicitly support non-standard toolchains: that's the responsibility of whoever needs them.

#### Q: (execution) Can we run Bazel within Bazel, or have a rule that can read the action graph? 
A ([@lberki](https://github.com/lberki)): Consider the remote execution API as an alternative to novel use of the build graph. It's hard to discuss more without more ues case details.  Running aquery under Ninja may also help (aquery exposes the action graph).

