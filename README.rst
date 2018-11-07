========
Pipstrap
========

Pipstrap is a small script that acts as a trust root for installing pip 8 or
greater.

Embed this in your project, and your VCS checkout is all you have to trust. In
a post-`peep <https://pypi.python.org/pypi/peep/>`_ era, this lets you claw
your way to a `hash-checking version of pip
<https://pip.readthedocs.org/en/stable/reference/pip_install/#hash-checking-
mode>`_, with which you can install the rest of your dependencies safely. All
it assumes is Python 2.6 or better and *some* trustworthy version of pip
already installed. If anything goes wrong, it will exit with a non-zero status
code.

Invoke it like this::

    my-virtual-env/bin/python my-project/pipstrap.py

At that point, pinned, hash-checked versions of pip, setuptools, and wheel will
be installed. (If your existing version of pip was already new enough, pipstrap
will have exited happily without doing anything.)

.. note::

    ``pip`` is invoked as a module of the python executable that was used to
    run pipstrap, so everything will be installed into that executable's
    environment.

How do I trust this?
====================

An astute question!

Pipstrap is short; read it. Validate its embedded hashes by downloading from
various locations (to defeat local MITMs), checking the PGP signature on pip,
comparing with version-control checkouts of all three packages, and talking
with friends. Check it into your project, sign your commits, and verify
signatures on deploy.

Also, if you trust me and my key-management practices, you can check any of the
release tags against my GPG signature.

Why?
====

* get-pip.py is yuckily large to embed, and it hits the network to fetch the
  latest pip, setuptools, and wheel, leaving the question of their genuineness
  up to HTTPS. (Its embedded pip is used only to download those new versions.)
* Continuing to embed peep just to bootstrap pip 8 is unwieldy, with an extra
  requirements file and a longer-than-necessary script. Plus, now that I've got
  hash-checking into pip, I don't want to continue maintaining a script that
  breaks whenever pip's private APIs change.

For how long?
=============

When your OS packages pip 8 or you otherwise get a copy of pip 8 you trust onto
your servers, you can dispense with pipstrap.

What about firewalls?
=====================

To use pipstrap from a machine that cannot access https://pypi.python.org/, set
the ``PIP_INDEX_URL`` environment variable to point to a PyPI mirror, either
public or internal. (See `the pip documentation on this option
<https://pip.pypa.io/en/stable/reference/pip_wheel/#cmdoption-i>`_ for
details.) Pipstrap needs a ``packages`` directory which is a copy of the one on
PyPI proper, and it will look for it in 2 places:

1. If ``PIP_INDEX_URL`` ends with "/simple" (as it often does in the real world
   to satisfy pip), pipstrap will look at ``${PIP_INDEX_URL}/../packages``. In
   other words, ``packages`` is expected to sit right next to ``simple``.
2. Otherwise, it will look at ``${PIP_INDEX_URL}/packages``.

Trust in the mirror is not necessary, as SHA-256 hash-checking suffices to
prove authenticity. Also, pipstrap doesn't care whether ``PIP_INDEX_URL`` has a
trailing slash.

Version History
===============

2.0
  * Execute pip as a module from the python executable used to call pipstrap,
    instead of resolving pip from the ``PATH``.

1.5.1
  * Revert our 2-phase installation procedure, which was causing setuptools not
    to be upgraded on Debian Wheezy. We now install a modern pip and setuptools
    all in one pip invocation. (The problem the 2-phase install was meant to
    solve has been fixed in recent versions of Ubuntu 16.04, and we don't know
    of any other distros having the problem.)

1.5
  * Update to setuptools 29.0.1, the newest version that doesn't drop support
    for any Python versions. This allows use of the ``python_requires`` keyword
    arg.

1.4
  * Add support for PyPI mirrors.
  * Do the installation in 2 phases: first pip; then argparse, wheel, and
    setuptools. This dodges an obscure bug:
    https://github.com/certbot/certbot/issues/4938. (bmw)

1.3
  * Update pip to 9.0.1 so we can support manylinux1 wheels.
  * Restore Python 2.6 compatibility.

1.2
  * Don't do anything if the pip version is already new enough.
  * Disable the pip cache to avoid ownership warnings, which don't apply since
    we're passing in files and not using the cache.
  * Fix a bytes/string mismatch under Python 3.

1.1.1
  * Under Python 2.6 don't pass the CalledProcessError exception the ``output``
    kwarg, which hasn't been invented yet.

1.1
  * Support Python 2.6. I feel so dirty, but Let's Encrypt needs it.
  * Update to pip 8.0.3, wheel 0.29, and setuptools 20.2.2.

1.0.1
  * Make flake8-compliant so you can embed it without having to make exceptions
    for it.

1.0
  * Initial release. Before I signed the release, I verified all 3 embedded
    hashes from various network locations and the pip one against dstufft's GPG
    signature.
