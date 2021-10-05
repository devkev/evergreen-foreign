evergreen-foreign
=================

This is a wrapper script around the [evergreen](https://github.com/evergreen-ci/evergreen) CLI tool that allow submitting patch builds from a "foreign" repository, that is, one which logically contains the code for the project, but with modified git history (such that the local commit ids are different to those in the actual project repository).

For example, this may be necessary if the project has historical commits that are too large to be pushed to Github, and it's not appropriate to use a branch or fork of the actual project Github repo.


Installation
------------

1. Install [shwrapnel](https://github.com/devkev/shwrapnel) by copying (or linking) the `shwrapnel` script somewhere in your `$PATH`.  (It does not need to be executable.)

2. Ensure that the actual `evergreen` cli tool is already installed somewhere in your `$PATH`.

3. Install the [evergreen](./evergreen) wrapper script by copying (or linking) it to an _*earlier*_ location in your `$PATH`.  If necessary, make it executable with `chmod a+x`.


Usage
-----

Most invocations of the `evergreen` wrapper will be passed through verbatim, and have unchanged behaviour.

The wrapper allows passing a new `--common-point` argument to `evergreen patch`, which indicates the git commit ref to be used as the base commit for the patch.  The commit message for this ref will be scanned, and the first git commit hash (40 hex digits) found is treated as the corresponding git commit hash in the actual project repo.

