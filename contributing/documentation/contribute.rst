Contribute to the documentation
===============================

If you want to contribute, you're really welcome!

Setup your environment
----------------------

To work on Fridge documentation, you need to setup a friendly environment with Git, Sphinx and additionally ApiGen. The
procedure is described :doc:`here <overview>`.

Git
^^^

The Fridge DBAL uses `Git`_ as version control system. Your should set up your user information with your real name and
a working email address:

.. code-block:: bash

    $ git config --global user.name "Your real name"
    $ git config --global user.email email@example.com

.. tip::

    If you are new to Git, we highly recommend you to read the excellent and free `ProGit`_ book.

Source documentation
^^^^^^^^^^^^^^^^^^^^

The source documentation is hosted on `GitHub`_. To get it locally, you need to:

* Create an `account`_ & `sign in`_.
* Fork the `fridge-project/dbal-docs`_ repository.
* Clone locally your fork:

    .. code-block:: bash

        $ git clone git@github.com:USERNAME/dbal-docs.git

* Add the upstream repository as remote:

    .. code-block:: bash

        $ git remote add upstream git://github.com/fridge-project/dbal-docs.git

Sphinx
^^^^^^

Now you've got the documentation locally, you should execute the sphinx quick setup which will create the ``config.py``
& the ``make`` files:

.. code-block:: bash

    $ sphinx-quickstart

.. note::

    This command will ask you some informations. Respond with default options for all.

To build the documentation, you can execute:

.. code-block:: bash

    $ make html

.. note::

    The documentation is available in the generated ``build/html`` directory.

Contribute
----------

The License
^^^^^^^^^^^

Before you start, you must know that all patches you are going to submit must be released under the
**Creative Commons Attribution-Share Alike 3.0 Unported License**. For more informations, you can see the
:doc:`LICENSE <license>`.

Create a branch
^^^^^^^^^^^^^^^

Each time you want to contribute, create a new branch:

.. code-block:: bash

    $ git checkout -b BRANCH_NAME master

.. tip::

    Use a descriptive name for your branch (``fix-xxx`` or ``rewrite-xxx`` for example).

Create your patch
^^^^^^^^^^^^^^^^^

Work on the documentation & commit as much as you want but keep in mind the following:

* Follow the :doc:`standards <standard>`.
* Do atomic and logical separate commits.
* Squash irrelevant commits.
* Write good commit message.

Submit your contribution
------------------------

When you feel your contribution is ready for submission, you can follow these steps:

Rebase your work
^^^^^^^^^^^^^^^^

Before submitting your contribution, you should rebase your branch:

.. code-block:: bash

    $ git checkout master
    $ git pull --rebase upstream/master master
    $ git checkout BRANCH_NAME
    $ git rebase master

When running the last command, you might have to fix conflicts. ``git status`` will show you the unresolved files.
Resolve all conflicts and then continue the rebase:

.. code-block:: bash

    $ git add ... # add resolved files
    $ git rebase --continue

Check the documentation is well builded by Sphinx. Then, push your branch remotely:

.. code-block:: bash

    $ git push origin BRANCH_NAME

Make a pull request
^^^^^^^^^^^^^^^^^^^

You're now able to make a pull request on the `fridge-project/dbal-docs`_ repository.

To ease the Fridge team work, always include the modified part in your pull request title:

.. code-block:: text

    [Book][Introduction] Add something
    [Contribution][Bug] Fix something

.. note::

    Please use the tag [WIP] in the pull request title if it is not yet finished.

Feedback
--------

After submitting your pull request, the team will review it. According to our feedbacks, you might need to rework it.
Before re-submitting it, rebase with ``upstream/master`` (**don't merge**) and force the push to the origin:

.. code-block:: bash

    $ git rebase -f upstream/master
    $ git push -f origin BRANCH_NAME

The Fridge team can ask you to "squash" your commits. That means you need to convert many commits to one commit. To do
this, use the interactive mode of the rebase command.

.. _Git:                      http://git-scm.com/
.. _ProGit:                   http://progit.org/
.. _GitHub:                   https://github.com/
.. _account:                  https://github.com/signup/free
.. _sign in:                  https://github.com/login
.. _fridge-project/dbal-docs: https://github.com/fridge-project/dbal-docs
