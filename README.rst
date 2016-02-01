========
Pipstrap
========

Pipstrap is a small script that acts as a trust root for installing pip 8.

Embed this in your project, and your VCS checkout is all you have to trust. In
a post-`peep <https://pypi.python.org/pypi/peep/>`_ era, this lets you claw
your way to a `hash-checking version of pip
<https://pip.readthedocs.org/en/stable/reference/pip_install/#hash-checking-
mode>`_, with which you can install the rest of your dependencies safely. All
it assumes is Python 2.7 or better and *some* trustworthy version of pip
already installed. If anything goes wrong, it will exit with a non-zero status
code.

Invoke it like this::

    source my-virtual-env/bin/activate
    python my-project/pipstrap.py

At that point, pinned, hash-checked versions of pip, setuptools, and wheel will
be installed.

.. note::

    Pipstrap calls ``pip`` via the shell, so whichever environment corresponds
    to the foremost pip on the ``$PATH`` is the one things will get installed
    into.

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
your servers, you can dispense with Pipstrap.

Version History
===============

1.0.1
  * Make flake8-compliant so you can embed it without having to make exceptions
    for it.

1.0
  * Initial release. Before I signed the release, I verified all 3 embedded
    hashes from various network locations and the pip one against dstufft's GPG
    signature.
