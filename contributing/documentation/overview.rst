Overview
========

A documentation is maybe more important than the code itself especially for open source projects. It tries to expose
all available features keeping in mind readability, testability, maintainability, extensibility, optimization and
refactoring. Anyway, documentation has bugs, typos, etc... So, your contribution is really welcome.

Sphinx
------

All fridge projects use `Sphinx`_ to build there documentations. Sphinx is a tool that makes it easy to create
intelligent and beautiful documentation with the `reStructuredText`_ markup language.

To install it, you need to get it from the `Python Package Index`_ or install it with:

.. code-block:: bash

    $ easy_install -U Sphinx

Now you've got Sphinx, you can :doc:`contribute <contribute>`!

ApiGen
------

`ApiGen`_ is a tool for creating professional API documentation from PHP source code. It has support for PHP 5.3
namespaces, packages, linking between documentation, cross referencing to PHP standard classes and general
documentation, creation of highlighted source code and more.

To install it:

.. code-block:: bash

    $ sudo pear config-set auto_discover 1
    $ sudo pear install pear.apigen.org/Apigen

To build the API documentation, you just need to run:

.. code-block:: bash

    $ apigen --source ./ --destination api --exclude */vendor/* --exclude */tests/*

If you found a typo in the phpDoc, please follow this :doc:`procedure </contributing/development/patch>`.

.. _Sphinx:               http://sphinx.pocoo.org/
.. _reStructuredText:     http://docutils.sourceforge.net/rst.html
.. _Python Package Index: http://pypi.python.org/pypi/Sphinx
.. _ApiGen:               http://apigen.org/
