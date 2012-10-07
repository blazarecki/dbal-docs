Run the test suite
==================

The Fridge DBAL is fully unit tested by `PHPUnit`_ with a code coverage close to **100%**. The Fridge team provides
tests as much as possible to maintain quality & avoid regressions.

PHPUnit
-------

To run the Fridge DBAL test suite, your need to install PHPUnit:

.. code-block:: bash

    $ sudo pear config-set auto_discover 1
    $ sudo pear install pear.phpunit.de/PHPUnit

Composer
--------

The Fridge DBAL uses `Composer`_ to manage dependencies. It allows you to declare the dependent libraries your project
needs and it will install & autoload them for you.

Composer comes with a simple phar file. To easily access it from anywhere on your system, you can execute:

.. code-block:: bash

    $ curl -s https://getcomposer.org/installer | php
    $ sudo mv composer.phar /usr/local/bin/composer

Then, just run composer:

.. code-block:: bash

    $ composer update --dev

Running
-------

Now you have installed PHPUnit & the Fridge DBAL dependencies, you can run the test suite:

.. code-block:: bash

    $ phpunit

If all is fine, the output should display OK. If not, you need to figure out what's going on...

.. note::

    With the default configuration, PHPUnit only executes tests which don't need database connection.

Configure connections
---------------------

To execute the full test suite, you need to configure database connections with your own PHPUnit configuration file:

.. code-block:: bash

    $ cp phpunit.xml.dist phpunit.xml

The default configuration comes with all testable connections. To enable one of them, you just need to uncomment &
configure it with your own values. **Then you just need to run phpunit.**

.. note::

    The Fridge DBAL test suite will automatically create/drop databases needed by tests.

Code coverage
-------------

The PHPUnit ``coverage-html`` option allows to know what parts of the code are executed when tests are run.
It helps answering questions such as:

* How do you find code that is not yet tested?
* How do you measure testing completeness?

.. code-block:: bash

    $ phpunit --coverage-html ./report

The command generates a full report. You can access it by opening the ``report/dashboard.html`` or ``report/index.html``
file in your browser.

.. tip::

    The code coverage only works if you have XDebug enabled.

.. _PHPUnit:         http://www.phpunit.de/manual/current/en/index.html
.. _Composer:        http://getcomposer.org/
