Introduction
============

The Fridge DBAL is a modern and powerfull PHP 5.3+ Database Abstraction Layer created by Eric GELOEN & sponsored by
`Wid'op`_.

Database Abstraction Layer?
---------------------------

A database abstraction layer is a library which unifies the communication between an application and a database.

PHP 5.3+ offers a lot of `database extensions`_ which provide their own interface which leaves it to the developer to
implement. Unfortunately, each extension have not the same API than the others...

The Fridge DBAL simply smouth out these differences by providing a consistent API to the developer and hide the
database specifics behind this interface as much as possible.

Philosophy
----------

The Fridge DBAL follows simple rules in order to maintain an high quality service:

* Follow best practices & KISS concept (Keep It Simple, Stupid!).
* Keep separation of concerns.
* Add tests as much as possible.

Overview
--------

The main point is the Fridge DBAL is build around a `PDO-like API`_. That means it can be adpated to all database
extensions and obviously, anyone aware of `PDO`_ is already familiar with it.

Anyway, the Fridge DBAL is much more than a simple library allowing to deal with querying, it offers a decoupled
architecture which gives you the power on multiple database aspects. Just to name a few:

* Nested transactions.
* Prepared statements.
* Query/Expression builder.
* Types support.
* Platform definitions.
* Shema manipulations.
* Debugging.
* Logging.
* Events.

An much more...

.. _Wid'op:              http://www.widop.com
.. _database extensions: http://php.net/manual/en/refs.database.php
.. _PDO-like API:        /dbal/api/namespace-Fridge.DBAL.Adapter.html
.. _PDO:                 http://php.net/manual/en/book.pdo.php
