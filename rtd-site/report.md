# Testify API Reference — maintainer-facing gaps

Live site: https://testify-docs.readthedocs.io/en/latest/
Source: https://github.com/stretchr/testify at pinned commit `87a7b9d57689f6579db2da795ab8deeab29cb724` (MIT)
Generated with: Sourcey 3.9.5, godoc adapter (live mode), via the sealed runx skill run `sourcey-testify-docs-2` (receipt `PENDING-NEW-RUNX-RECEIPT`)

The generated reference is accurate — 220 unique exported functions and types, real signatures and doc comments taken from the pinned source, 13 pages, live and navigable. What follows is what a maintainer would still want fixed before linking this as the reference for the project.

## Gaps

- **No per-symbol source links to the pinned commit.** A reader cannot jump from a function (for example `assert.Equal`) to its exact file and line on GitHub at the pinned commit; the pages carry the module path as text but no clickable source links, so verifying a signature against the source takes manual searching.
- **Examples are thin.** Only the suite page renders `Example*` functions (5 mentions in the whole site); assert, require, and mock show signatures with no runnable examples, so large parts of the reference read as a signature list rather than documentation a newcomer can copy from.
- **Internal packages surface as top-level navigation.** `internal/spew`, `internal/difflib`, and `assert/internal/unsafetests` appear alongside the public packages even though they are not public API; a maintainer linking this would want them hidden or clearly marked internal so readers do not import them.
- **No assert-vs-require guide.** The two packages mirror each other function for function, and the site never explains the one decision that matters — `require` aborts the test on failure while `assert` continues — so a newcomer has to discover it from elsewhere.
- **No mock lifecycle guide.** `Mock.Called`, `AssertExpectations`, and `Arguments` are listed per-method with no ordering or worked example of a complete mock test, which is the part of the mock package people actually get wrong.
- **No suite lifecycle guide.** The `Suite` methods (`SetupSuite`, `SetupTest`, `TearDownTest`, and friends) are documented individually but never as the sequence they run in, so the ordering contract is left implicit.
- **The home page has no package map.** A stranger landing on the home page gets the project description but no inventory of the 9 packages; they must open the Go API tab and guess which package holds what.
- **One pinned commit, no versioning.** There is no way to switch to another testify version or see what changed between releases; the site is a snapshot, not a versioned reference.
- **README guidance is absent.** The godoc adapter only covers Go source, so the project's own prose — migration notes, staying-up-to-date guidance, the README's examples — does not appear in the generated site.
- **The http package page is nearly empty.** It surfaces 2 types and 0 functions, and nothing on the page explains its purpose (testing HTTP handlers), so it reads as a stub rather than a package reference.
- **No cross-links between mirrored functions.** `assert.Equal`, `require.Equal`, and related helpers are never linked to each other, so a reader who finds one cannot jump to its counterpart.

## What is solid

- Signatures, doc comments, and package docs are taken directly from the pinned source — spot-checked `func Equal` on the assert page against the live site.
- Package pages cover the full public surface: assert (158 functions), require (153), mock, suite, http, plus internal helpers.
- The site ships `llms.txt`, a sitemap, and search, and every page loads over the public URL with working assets.

## Suggested next steps for a maintainer

- Add per-symbol source links pinned to the commit in the godoc adapter output.
- Exclude `internal/*` packages from navigation (or label them internal) and add an authored assert-vs-require guide plus a mock/suite lifecycle guide.
- Add a package inventory to the home page and, when a second version is generated, version switching.
