evergreen-foreign
=================

This is a wrapper script around the [evergreen](https://github.com/evergreen-ci/evergreen) CLI tool that allow submitting patch builds for a "foreign" repository, that is, one which logically contains the same code for the project, but has a modified or different git history - ie. the local commit ids are different to those in the "actual" project repository, even though the contents of the files are the same.

For example, this may be necessary if the actual project has historical commits that are now too large to be pushed to Github, and it's not appropriate to use a branch or fork of the project's actual Github repo.


Installation
------------

1. Install [shwrapnel](https://github.com/devkev/shwrapnel) by copying (or linking) the `shwrapnel` script somewhere in your `$PATH`.  (It does not need to be executable.)

2. Ensure that the actual `evergreen` cli tool is already installed somewhere in your `$PATH`.

3. Install the [evergreen](./evergreen) wrapper script by copying (or linking) it to an _*earlier*_ location in your `$PATH`.  If necessary, make it executable with `chmod a+x`.


Usage
-----

Most invocations of the `evergreen` wrapper will be passed through verbatim, and have unchanged behaviour.

1. **Manually specify local and foreign common points:**

    The wrapper allows passing a new `--common-point` argument to `evergreen patch`:
    ```
    evergreen patch ... --common-point local_common_point=foreign_common_point
    ```

    This indicates the patch should be taken against git commit ref `local_common_point` in the local tree, and that this commit corresponds to commit ref `foreign_common_point` in the "actual" upstream project repo.  For example:
    ```
    $ evergreen patch ... --common-point f0b65992790ddaa74698b2209cbcad91417327be=83972b91fc8c84f38217c36735cf0828888a774a
    ```

2. **Manually specify local common point, get foreign common point from commit message:**

    If the `=foreign_common_point` specifier is omitted, then the wrapper will scan the commit message of `local_common_point`, and use the first git commit hash (40 hex digits) as the `foreign_common_point`.  For example, this is equivalent to the previous example above:
    ```
    $ git show --no-patch f0b65992790ddaa74698b2209cbcad91417327be
    commit f0b65992790ddaa74698b2209cbcad91417327be
    Author: Kevin Pulo <kevin.pulo@mongodb.com>
    Date:   Sat Aug 28 14:49:09 2021 +1000

        mongodb/mongo as of 83972b91fc8c84f38217c36735cf0828888a774a

    $ evergreen patch ... --common-point f0b65992790ddaa74698b2209cbcad91417327be
    ```

3. **Automatically find local and foreign common points:**

    If the `local_common_point` is a parentless commit, and has a commit message that contains a git commit hash (40 hex digits), then it is possible to use `--foreign` instead of `--common-point`.  For example, this is equivalent to the previous example above:
    ```
    $ git log | tail -6
    commit f0b65992790ddaa74698b2209cbcad91417327be
    Author: Kevin Pulo <kevin.pulo@mongodb.com>
    Date:   Sat Aug 28 14:49:09 2021 +1000

        mongodb/mongo as of 83972b91fc8c84f38217c36735cf0828888a774a

    $ evergreen patch ... --foreign
    ```

