========
Pipstrap
========
----------------------------------------------------------------
A small script that can act as a trust root for installing pip 8
----------------------------------------------------------------

Embed this in your project, and your VCS checkout is all you have to trust. In
a post-`peep <https://pypi.python.org/pypi/peep/>`_ era, this lets you claw
your way to a `hash-checking version of pip
<https://pip.readthedocs.org/en/stable/reference/pip_install/#hash-checking-
mode>`_, with which you can install the rest of your dependencies safely. All
it assumes is Python 2.7 or better and *some* version of pip already installed.
If anything goes wrong, it will exit with a non-zero status code.
