Submit a patch
==============

If you want to provide a new feature or a bug fix, patches are what you are looking for.

Setup your environment
----------------------

To work on Fridge, you need to setup a friendly environment with the following softwares:

* Git
* PHP 5.3.3+
* Composer
* PHPUnit

Additionally, you should install supported DBMS:

* MySQL
* PostgreSQL

Git
^^^

The Fridge DBAL uses `Git`_ as version control system. Your should set up your user information with your real name and
a working email address:

.. code-block:: bash

    $ git config --global user.name "Your real name"
    $ git config --global user.email email@example.com

.. tip::

    If you are new to Git, we highly recommend you to read the excellent and free `ProGit`_ book.

Source code
^^^^^^^^^^^

The source code is hosted on `GitHub`_. To get it locally, you need to:

* Create an `account`_ & `sign in`_.
* Fork the `fridge-project/dbal`_ repository.
* Clone locally your fork:

    .. code-block:: bash

        $ git clone git@github.com:USERNAME/dbal.git

* Add the upstream repository as remote:

    .. code-block:: bash

        $ git remote add upstream git://github.com/fridge-project/dbal.git

Run the test suite
^^^^^^^^^^^^^^^^^^

Now you've got the Fridge DBAL locally, you need to check that all unit tests pass for your environment as explained in
this dedicated :doc:`documentation <test>`.

Work on your patch
------------------

The License
^^^^^^^^^^^

Before you start, you must know that all patches you are going to submit must be released under the **MIT license**.
For more informations, you can see the :doc:`LICENSE <license>`.

Create a branch
^^^^^^^^^^^^^^^

Each time you want to work on a patch, create a new branch:

.. code-block:: bash

    $ git checkout -b BRANCH_NAME master

.. tip::

    Use a descriptive name for your branch (``fix-xxx`` or ``my-new-feature`` for example).

Create your patch
^^^^^^^^^^^^^^^^^

Work on the code & commit as much as you want but keep in mind the following:

* Follow the :doc:`coding standards <standard>`.
* Add :doc:`unit tests <test>` as much as possible.
* Do atomic and logical separate commits.
* Squash irrelevant commits.
* Fix coding standards in isolated branch.
* Write good commit message.

Submit your patch
-----------------

When you feel your patch is ready for submission, you can follow these steps:

Rebase your patch
^^^^^^^^^^^^^^^^^

Before submitting your patch, you should rebase your branch:

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

Check all tests still pass and push your branch remotely:

.. code-block:: bash

    $ git push origin BRANCH_NAME

Make a pull request
^^^^^^^^^^^^^^^^^^^

You're now able to make a pull request on the `fridge-project/dbal`_ repository.

To ease the Fridge team work, always include the modified class in your pull request title:

.. code-block:: text

    [Connection] Add something
    [Schema][ForeignKey] Fix something

In the pull request description, give as much details as possible about your changes.

.. note::

    Please use the tag [WIP] in the pull request title if it is not yet finished.

.. note::

    We really encourage you to check if your changes need an other pull request on the `documentation repository`_ .

Feedback
--------

After submitting your patch, the team will review it. According to our feedbacks, you might need to rework your patch.
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
.. _fridge-project/dbal:      https://github.com/fridge-project/dbal
.. _documentation repository: https://github.com/fride-project/dbal-docs
