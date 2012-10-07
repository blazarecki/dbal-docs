Installation
============

To install the Fridge DBAL, you will need `Composer`_. It's a PHP 5.3+ dependency manager which allows you to declare
the dependent libraries your project needs and it will install & autoload them for you.

Set up Composer
---------------

Composer comes with a simple phar file. To easily access it from anywhere on your system, you can execute:

.. code-block:: bash

    $ curl -s https://getcomposer.org/installer | php
    $ sudo mv composer.phar /usr/local/bin/composer

Define dependencies
-------------------

Create a ``composer.json`` file at the root directory of your project and simply require the ``fridge/dbal`` package:

.. code-block:: yaml

    {
        "require": {
            "fridge/dbal": "dev-master"
        }
    }

Install dependencies
--------------------

Now, you have define your dependencies, you can install them:

.. code-block:: bash

    $ composer install

.. note::

    Composer will automatically download your dependencies & create an autoload file in the ``vendor`` directory.

Autoload
--------

So easy, you just have to require the generated autoload file and you are already ready to play:

.. code-block:: php

    <?php

    require __DIR__.'/vendor/autoload.php';

    use Fridge\DBAL;

    // ...

.. note::

    The Fridge DBAL follows the `PSR-0 Standard`_. If you prefer install it manually, it can be autoload by any
    convenient autoloader.

.. _Composer:       http://getcomposer.org
.. _PSR-0 Standard: https://github.com/php-fig/fig-standards/blob/master/accepted/PSR-0.md
