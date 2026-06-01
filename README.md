## What is this?

This is an 'umbrella' project for custom DMD releases, consisting
of DMD/Phobos/dub/reggae/ninja submodules and a CI script for
building and publishing binary DMD packages similar to the official
ones.

## Why?

The main motivation is enabling releases at arbitrary points in time,
primarily from DMD's `stable` branch. For instance, if an official
release comes with deal-breaking regressions and needs a fixup for
us to be usable, we can easily release our own version once the fix
has landed upstream.

## Package differences from upstream

- Linux x64 only. No bundled 32-bit executables/libs.
- Additionally bundled `reggae` tool (like LDC).
- Additionally bundled `ninja` tool, incl.
  - https://github.com/symmetryinvestments/ninja/pull/1 for a central
    shared build artifacts cache
  - https://github.com/ninja-build/ninja/pull/2019 for adaptive scheduling,
    and https://github.com/symmetryinvestments/ninja/pull/2 to make
    scheduling based on previous build times opt-in via `--cp`, as it may
    require huge peak RAM
- No full compiler/druntime/Phobos sources.
- No docs (HTML/man), no samples, just plain `make install`.
- Different build environment, build options...

## Proposed workflow for a bump

1. `git fetch` all submodules.
2. Advance DMD/Phobos submodules to some tag or `stable` head.
   (They need to be in sync; DMD doesn't have submodules, so the same
   point in time is the sync reference point.)
3. Major bump: advance dub/reggae submodules to latest stable versions.
   Optionally bump host D compiler in `.gitlab-ci.yml`.
4. Commit changes and open a PR. Download the `dmd2.tar.xz` CI artifact
   for local testing. Merge when happy.
   * To mimic a compiler install from the official [install.sh](https://dlang.org/install.html)
     script:
     1. Unpack the downloaded CI artifact into `~/dlang/`.
     2. Rename the just-extracted `~/dlang/dmd2` dir to e.g. `dmd-2.108.0-sym1`.
     3. Copy the `activate` script from another DMD subdir in `~/dlang/`
        to the new dir and fix up the compiler directory/version.
6. Sync & push a new tag with shape `v2.108.0-sym1`. CI creates a
   [GitHub release](https://github.com/symmetryinvestments/dmd-umbrella/releases)
   and uploads the artifact.
