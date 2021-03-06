# sortest

**Continuous testing in Python** with test sorting by execution speed,
  and with auto-restart from the beginning when files change.

For about nine months I have been using a [continuous testing
tool](https://github.com/eigenhombre/continuous-testing-helper/) I
wrote called `conttest`, in conjunction with
[Nose](https://nose.readthedocs.org/en/latest/), for continuous
testing of Python code.  Depending on what I'm working on, 
`conttest` automagically runs one, some or all of my automated tests,
whenever I save a file in my source tree.

The problem with this approach is that I either have to specify what
test I want it to run (very fast, but requires detailed work at the
command line), or let it run all my tests, which at the moment clock
upwards of ten minutes (far too slow to get immediate feedback). Since
I favor a full-on TDD approach, this is an unhappy choice to have to
make. When I start writing a new test, I want to know immediately when
that test passes or succeeds. And, when I'm thinking and typing,
there's no reason my slower tests shouldn't continue to run, finding
failures in slow tests or in [tests which only fail
occasionally](http://stackoverflow.com/questions/13611658/repeated-single-or-multiple-tests-with-nose).


As a result of my experiences with
this approach, I want:

1. To have a program discover all the tests I have to run;
1. To stop at the first failure;
1. To always run the last failed test first;
1. If there is no previous test failure, **run the fastest tests first**;
1. Even if the tests aren't done yet, **restart the testing from the
   beginning** if I change any source files.

The first three, I can get Nose to do no problem.  I tried to write a
plugin for Nose to do Items 4 and 5, to no avail -- I suspect only
the God of Noses can do such a thing.

My answer is `sortest`, which meets these requirements.  `sortest`
borrows a module-loading utility from Nose (and therefore has `nose`
as a requirement) but otherwise stands alone.

The first time through, tests are run in discovery order, but `sortest`
remembers the test speeds for subsequent passes, and runs the fastest
ones first after that (assuming no tests fail).

## Installation

    pip install sortest  # Or easy_install sortest

## Using sortest

At the bash prompt:

    cd /path/to/my/great/source/code
    sortest

Usage:

    usage: sortest [-h] [-v | -q] [-f EXCLUDE_FILE] [-d EXCLUDE_DIR] [-n]
                   [rootdir1] [rootdir2] ...

    positional arguments:
      rootdirX              One or more directories to search in and to perform 
                            tests on (default=current working directory)

    optional arguments:
      -h, --help            show this help message and exit
      -v, --verbose
      -q, --quiet
      -f EXCLUDE_FILE, --exclude-file EXCLUDE_FILE.  Can be a (Python-syntax) regex.
      -d EXCLUDE_DIR, --exclude-dir EXCLUDE_DIR
      -n, --dry-run


You can also call the `sortest` test infrastructure from inside your Python code:

    import sortest

    rootdir = os.path.join(*(["/"] +
                             os.path.dirname(__file__).split('/')[:-1]))
    dirlist = [rootdir]  # Can add more directories if desired...

    excluded_files = ["__init__.py", "fabfile.py", "setup.py"]  # Names or regex's
    excluded_dirs = ['.svn', '.git', 'man', 'migrations']
    sortest.continuously_test(dirlist, excluded_files,
                              excluded_dirs, verbose_level=1)

## Requirements

Tested only on Python 2.6 so far.  Depends on the Nose package for package importing.

## Author

[John Jacobsen](http://eigenhombre.com), [NPX Designs](http://npxdesigns.com), Inc.

## To Do

1. Add `@first` and `@last` decorators, which will put the decorated
test functions first or last in the list of tests, overriding sort
order (except failed tests still run first).
1. Tests in subclasses of `unittest.TestCase` get run in sub-subclasses as well, and should not.
1. Code could stand some more comments and cleanup.
1. Options are limited compared to Nose (though I may keep it simpler
than Nose and `unittest`). I am considering allowing plugins for:
    - Global test setup (e.g. for a Django database harness)
    - Test discovery
    - Deciding test order
    - Deciding stop criteria
    - Deciding restart criteria
1. More unit tests!


## Caveat

This is VERY alpha software.  DON'T USE IT YET.  I cannot be held
liable for any missiles launched or life support systems crashed
because you used this completely unsupported and brand-new software.

In particular, be advised that ANY function named `test*` in
directories/files not explicitly excluded will be run. So rename
those `test_launch_missiles`, `test_database_destroy` and
`test_turn_off_life_support` functions.

## License

Copyright © 2012 John Jacobsen

Distributed under the Eclipse Public License.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND,
EXPRESS OR IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF
MERCHANTABILITY, FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT
OF THIRD PARTY RIGHTS. IN NO EVENT SHALL THE AUTHORS OR COPYRIGHT
HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER LIABILITY, WHETHER
IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM, OUT OF OR
IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
SOFTWARE.
