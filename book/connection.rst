Connection
==========

The Connection is the central point in the library. It simply gives you access to all available Fridge DBAL features.

Get a Connection
----------------

To get a Fridge Connection, you need the ``Fridge\DBAL\Factory``. It gives you a ``Fridge\DBAL\Connection\Connection``
which will allow you to do what you want with your database.

.. code-block:: php

    use Fridge\DBAL\Factory;

    $parameters = array(
        'driver'   => 'pdo_mysql',
        'username' => 'user',
        'password' => 'pass',
        'dbanme'   => 'dbname',
    );

    $connection = Factory::getConnection($parameters);

The factory needs an array of parameters with a unique mandatory parameter: **driver**.

Build-in Drivers
^^^^^^^^^^^^^^^^

Each driver gives you access to additional parameters in order to describe your database connection.

Mysqli
~~~~~~

The mysqli extension allows you to access the functionality provided by MySQL 4.1 and above. If you are using MySQL
versions 4.1.3 or later it is **strongly** recommended that you use this extension.

The available parameters are:

.. code-block:: php

    $parameters = array(
        'driver'      => 'mysqli',
        'username'    => 'user',
        'password'    => 'pass',
        'dbanme'      => 'dbname',
        'host'        => 'localhost',
        'port'        => 3306,
        'unix_socket' => '/tmp/mysql.sock',
        'charset'     => 'utf-8',
    );

PDO MySQL
~~~~~~~~~

PDO MySQL is a driver that implements the PHP Data Objects (PDO) interface to enable access to MySQL 3.x, 4.x and 5.x
databases.

The available parameters are:

.. code-block:: php

    $parameters = array(
        'driver'         => 'pdo_mysql',
        'username'       => 'user',
        'password'       => 'pass',
        'dbanme'         => 'dbname',
        'host'           => 'localhost',
        'port'           => 3306,
        'unix_socket'    => '/tmp/mysql.sock',
        'charset'        => 'utf-8',
        'driver_options' => array(),
    );

PDO PostgreSQL
~~~~~~~~~~~~~~

PDO PostgreSQL is a driver that implements the PHP Data Objects (PDO) interface to enable access to PostgreSQL
databases.

The available parameters are:

.. code-block:: php

    $parameters = array(
        'driver'         => 'pdo_pgsql',
        'username'       => 'user',
        'password'       => 'pass',
        'dbanme'         => 'dbname',
        'host'           => 'localhost',
        'port'           => 5432,
        'driver_options' => array(),
    );

Configuration
^^^^^^^^^^^^^

The Factory accepts a second parameter which is a ``Fridge\DBAL\Configuration``. It allows you to configure the
internal `Logger`_ (Monolog) & `Event Dispatcher`_ (Symfony2).

.. code-block:: php

    use Fridge\DBAL;

    $configuration = new DBAL\Configuration();

    // Customize the configuration

    $parameters = array(
        // ..
    );

    $connection = DBAL\Factory::getConnection($parameters, $configuration);

Extensibility
^^^^^^^^^^^^^

Driver
~~~~~~

A custom driver implementation can allow to define your own adapter, platform or schema manager implementation.

.. code-block:: php

    use Fridge\DBAL\Factory;

    $parameters = array(
        'driver_class' => 'My\Namespace\Driver',
        // ..
    );

    $connection = Factory::getConnection($parameters);

.. note::

    Your class must implement ``Fridge\DBAL\Driver\DriverInterface``.

.. note::

    The mandatory driver parameter is not needed anymore and will be ignored anyway.

Connection
~~~~~~~~~~

A custom connection implementation will allow you to override all other features not covered by a custom driver.

.. code-block:: php

    use Fridge\DBAL\Factory;

    $parameters = array(
        'connection_class' => 'My\Namespace\Connection',
        // ..
    );

    $connection = Factory::getConnection($parameters);

.. note::

    Your class must implement ``Fridge\DBAL\Connection\ConnectionInterface``.

Query your Database
-------------------

Retrieve datas is one of the primary use-case of a database. The Fridge DBAL offers a powerfull & secure API around a
PDO-like one to allow you to request your database easily.

Simple Statement
^^^^^^^^^^^^^^^^

The query method is the most simple one for fetching data. It executes the SQL and returns a statement.

.. code-block:: php

    $statement = $connection->query('SELECT firstname, lastname FROM user');

It also has several drawbacks:

* This is not possible to add dynamic parameters to the SQL query without modifying it. This can easily lead to a
  potential security issue called `SQL injection`_.
* Quoting dynamic parameters for an SQL query is tedious work and requires lot of use of the
  ``Fridge\DBAL\Connection\Connection#quote()`` method...
* Each time you query your database, it will analyze, compile and optimize your query. Using this method will trigger
  this process over and over again.

.. tip::

    This method should only be used without dynamic parameters in order to avoid :doc:`security<security>` issues.

Prepared Statement
^^^^^^^^^^^^^^^^^^

Like describe in this very good `article`_, a prepared statement has several advantages:

* Optimize query for reusability.
* Separate the SQL query from the input parameters.
* Automatize quoting.

A prepared statement separates the SQL query from the input parameters by requiring the developer to add placeholders
to the SQL query. There are two kinds of placeholders which can not be mixed together: positional & named.

.. note::

    If the underlying driver does not support one of the two, the Fridge DBAL will emulate it.

Positional Placeholder
~~~~~~~~~~~~~~~~~~~~~~

A positional placeholder is defined by a question mark (?). This approach is called positional, because the values are
bound in order from left to right beginning by one to any question mark found in the SQL query.

.. code-block:: php

    $query = 'SELECT firstname, lastname FROM user WHERE civility = ? AND enabled = ?';

    $statement = $connection->prepare($query);
    $statement->bindValue(1, 'm');
    $statement->bindValue(2, true);
    $statement->execute();

Named Placeholder
~~~~~~~~~~~~~~~~~

A named placeholder is defined by a simple string starting with a double-colon (:placeholder). This approach have the
advantage that placeholders can be re-used and only need to be bound once.

.. code-block:: php

    $query = 'SELECT firstname, lastname FROM user WHERE civility = :civility AND enabled = :enabled';

    $statement = $connection->prepare($query);
    $statement->bindValue('civility', 'm');
    $statement->bindValue('enabled', true);
    $statement->execute();

Type Support
~~~~~~~~~~~~

When you bind a value on your prepared statement, you can specify a third argument in order to typehint your value. You
can use ``PDO::PARAM_*`` types and/or any :doc:`Fridge DBAL types<type>`.

.. code-block:: php

    $query = 'SELECT firstname, lastname FROM user WHERE enabled = ? and created_at > ?';

    $statement = $connection->prepare($query);
    $statement->bindValue(1, true, \PDO::PARAM_BOOL);
    $statement->bindValue(2, new \DateTime('-2 months'), 'datetime');
    $statement->execute();

Fetching
^^^^^^^^

To retrive datas, you need to fetch them on your statement. All fetch methods accepts a parameter which describes the
fetching strategy. The Fridge DBAL supports ``PDO::FETCH_NUM``, ``PDO::FETCH_ASSOC`` & ``PDO::FETCH_BOTH`` strategy.

Fetch All
~~~~~~~~~

The fetch all method retrieves all rows from the statement.

.. code-block:: php

    $result = $statement->fetchAll(\PDO::FETCH_NUM);

    /*
        $result = array(
            array(0 => 'Éric', 1 => 'Geloen'),
            array(0 => 'Clément', 1 => 'Herremman'),
        );
    */

.. code-block:: php

    $result = $statement->fetchAll(\PDO::FETCH_ASSOC);

    /*
        $result = array(
            array('firstname' => 'Éric', 'lastname'  => 'Geloen'),
            array('firstname' => 'Clément', 'lastname' => 'Herreman'),
        );
    */

.. code-block:: php

    $result = $statement->fetchAll(\PDO::FETCH_BOTH);

    /*
        $result = array(
            array(
                'firstname' => 'Éric',
                'lastname'  => 'Geloen',
                0           => 'Éric',
                1           => 'Geloen',
            ),
            array(
                'firstname' => 'Clément',
                'lastname'  => 'Herreman',
                0           => 'Clément',
                1           => 'Herreman',
            ),
        )
    */

Fetch
~~~~~

The fetch method retrieves the next row from the statement or false if there are none. Moves the pointer forward one
row, so that consecutive calls will always return the next row.

.. code-block:: php

    $result = $statement->fetch(\PDO::FETCH_NUM);

    /*
        $result = array(
            0 => 'Éric',
            1 => 'Geloen',
        );
    */

.. code-block:: php

    $result = $statement->fetch(\PDO::FETCH_ASSOC);

    /*
        $result = array(
            'firstname' => 'Éric',
            'lastname'  => 'Geloen',
        );
    */

.. code-block:: php

    $result = $statement->fetch(\PDO::FETCH_ASSOC);

    /*
        $result = array(
            'firstname' => 'Éric',
            'lastname'  => 'Geloen',
            0           => 'Éric',
            1           => 'Geloen',
        );
    */

Fetch Column
~~~~~~~~~~~~

The fetch column retrieves only one column of the next row specified by column index. Moves the pointer forward one
row, so that consecutive calls will always return the next row.

.. code-block:: php

    $result = $statement->fetchColumn(0);

    /* $result = 'Éric'; */

.. code-block:: php

    $result = $statement->fetchColumn(1);

    /* $result = 'Geloen'; */

Fridge API
^^^^^^^^^^

Writing this piece of code can become a pain. The Fridge DBAL proposes the
``Fridge\DBAL\Connection\Connection::executeQuery`` method which can prepare, bind & execute all statements by a
single call.

Simple Statement
~~~~~~~~~~~~~~~~

If you want to get a simple statement, you just need to execute your query.

.. code-block:: php

    $statement = $connection->executeQuery('SELECT firstname, lastname FROM user');

Prepared Statement
~~~~~~~~~~~~~~~~~~

To build & execute a prepared statement, you need to specify all binded values to the method.

.. code-block:: php

    $statement = $connection->executeQuery(
        'SELECT firstname, lastname FROM user WHERE id = ?',
        array(5)
    );

    // OR

    $statement = $connection->executeQuery(
        'SELECT firstname, lastname FROM user WHERE id = :id',
        array('id' => 5)
    );

Type Support
~~~~~~~~~~~~

The Fridge API supports ``PDO::PARAM_*`` types and any :doc:`Fridge DBAL types<type>`.

.. code-block:: php

    $statement = $connection->executeQuery(
        'SELECT firstname, lastname FROM user WHERE created_at > ?',
        array(new \DateTime('-2 months'))
        array('datetime'),
    );

    // OR

    $statement = $connection->executeQuery(
        'SELECT firstname, lastname FROM user WHERE created_at > :createdAt',
        array('createdAt' => new \DateTime('-2 months'))
        array('createdAt' => 'datetime'),
    );

.. tip::

    You can process a partial type binding. That means if you execute a query with three placeholders, you can only
    typehint the first & third.

Query Rewriting (IN)
~~~~~~~~~~~~~~~~~~~~

Using a prepared statement & the ``IN`` SQL clause is really a pain... Take this simple query:

.. code-block:: php

    $query = 'SELECT firstname, lastname FROM user WHERE id IN (?, ?, ?, ?)';

    $statement = $connection->prepare($query);
    $statement->bindValue(1, 5);
    $statement->bindValue(2, 7);
    $statement->bindValue(3, 9);
    $statement->bindValue(4, 3);
    $statement->execute();

You must build you SQL query according to the IN items & bind them one by one...

The Fridge API allows to typehint binded values like an array of native type. That's mean you can define a unique
placeholder and then simply bind your values on it. To enable this feature, you need to add ``[]`` at the end of any
:doc:`Fridge DBAL types<type>`.

.. code-block:: php

    $statement = $connection->executeQuery(
        'SELECT firstname, lastname FROM user WHERE id IN (?)',
        array(array(5, 7, 9, 3)),
        array('integer[]')
    );

    // OR

    $statement = $connection->executeQuery(
        'SELECT firstname, lastname FROM user WHERE id IN (:ids)',
        array('ids' => array(5, 7, 9)),
        array('ids' => 'integer[]')
    );

.. note::

    The binded value must be an non empty array.

Fetching
~~~~~~~~

Instead of execute a query & get a prepared statement, you can directy fetch datas via the
``Fridge\DBAL\Connection\Connection::fetch*`` methods.

Fetch All
"""""""""

The fetch all method retrieves all rows using the ``PDO::FETCH_ASSOC`` strategy.

.. code-block:: php

    $query = 'SELECT firstname, lastname FROM user WHERE id = ?';

    $result = $connection->fetchAll($query, array($id), array('integer'));

    /*
        $result = array(
            array('firstname' => 'Éric', 'lastname' => 'Geloen'),
            array('firstname' => 'Clément', 'lastname' => 'Herreman'),
        );
    */

Fetch Assoc
"""""""""""

The fetch assoc method retrieves the first row using the ``PDO::FETCH_ASSOC`` strategy.

.. code-block:: php

    $query = 'SELECT firstname, lastname FROM user WHERE id = ?';

    $result = $connection->fetchAssoc($query, array($id), array('integer'));

    /*
        $result = array(
            'firstname' => 'Éric',
            'lastname'  => 'Geloen',
        );
    */

Fetch Array
"""""""""""

The fetch assoc method retrieves the first row using the ``PDO::FETCH_NUM`` strategy.

.. code-block:: php

    $query = 'SELECT firstname, lastname FROM user WHERE id = ?';

    $result = $connection->fetchArray($query, array($id), array('integer'));

    /*
        $result = array(
            0 => 'Éric',
            1 => 'Geloen',
        );
    */

Fetch Column
""""""""""""

The fetch column retrieves only one column of the first row specified by column index.

.. code-block:: php

    $query = 'SELECT firstname, lastname FROM user WHERE id = ?';

    $result = $connection->fetchColumn($query, array($id), array('integer'), 0);

    /* $result = 'Éric'; */

Update your Database
--------------------

One other primary use case of a database is the manipulation.The Fridge DBAL offers a powerfull & secure API around a
PDO-like one to allow you to update your database easily.

Simple Statement
^^^^^^^^^^^^^^^^

The exec method is the most simple one for manipulate data. It executes the SQL and returns the number of affected rows.

.. code-block:: php

    $affectedRows = $connection->exec('DELETE FROM user');

Prepared Statement
^^^^^^^^^^^^^^^^^^

.. code-block:: php

    $query = 'UPDATE user SET firstname = ?, lastname = ? WHE';

    $statement = $connection->prepare($query);
    $statement->bindValue(1, $firstname, \PDO::PARAM_STR);
    $statement->bindValue(2, $lastname, 'string');
    $statement->bindValue(3, $id);

    $affectedRows = $statement->execute();

.. note::

    An update prepared statement supports placeholders (named or positional) and types (PDO and/or
    :doc:`Fridge DBAL<type>`).

Fridge API
^^^^^^^^^^

Writing this piece of code can become a pain. The Fridge DBAL proposes the
``Fridge\DBAL\Connection\Connection::executeUpdate`` method which can prepare, bind & execute all statements by a
single call.

Execute Update
~~~~~~~~~~~~~~

.. code-block:: php

    $affectedRows = $connection->executeUpdate(
        'UPDATE user SET firstname = ?, lastname = ? WHERE id = ?',
        array($firstname, $lastname, $id),
        array(\PDO::PARAM_STR, 'string')
    );

.. note::

    The execute update supports placeholders (named or positional), types (PDO and/or :doc:`Fridge DBAL<type>`),
    partial type binding and query rewriting.

Insert
~~~~~~

The insert method allows you to insert datas easily. It takes in first parameter the table name, in second parameter an
associative array describing column <-> value pair and in third parameter, an optional associative array allowing you
to typehint your value.

.. code-block:: php

    $affectedRows = $connection->insert(
        'user',
        array('firstname' => 'Éric', 'lastname' => 'Geloen'),
        array('firstname' => \PDO::PARAM_STR, 'lastname' => 'string')
    );

Update
~~~~~~

The update method allows you to update datas easily. It takes in first parameter the table name, in second parameter an
associative array describing column <-> value pair and in third parameter, an optional associative array allowing you
to typehint your value.

.. code-block:: php

    $affectedRows = $connection->update(
        'user',
        array('firstname' => 'Éric', 'lastname' => 'Geloen'),
        array('firstname' => \PDO::PARAM_STR, 'lastname' => 'string'),
    );

If you want to filter your update, you can make an advanced update with the fourth, fifth & sixth parameters which
represent respectively the expression, the expression parameters & the expression parameters types.

.. code-block:: php

    $affectedRows = $connection->update(
        'user',
        array('firstname' => 'Éric', 'lastname' => 'Geloen'),
        array('firstname' => \PDO::PARAM_STR, 'lastname' => 'string'),
        'id = ?'
        array($id),
        array('integer')
    );

Delete
~~~~~~

The delete method allows you to delete datas easily. It takes in first parameter the table name.

.. code-block:: php

    $affectedRows = $connection->delete('user');


If you want to filter your deletion, you can make an advanced deletion with the second, third & fourth parameters which
represent respectively the expression, the expression parameters & the expression parameters types.

.. code-block:: php

    $affectedRows = $connection->delete(
        'user',
        'id = ?',
        array($id),
        array('integer')
    );

Transaction
-----------

The Fridge DBAL provides a fluent API for transaction management. Build around a PDO-like API, if follows the same
principle with the methods ``beginTransaction``, ``commit`` & ``rollback``.

Simple transaction
^^^^^^^^^^^^^^^^^^

A transaction looks as follows:

.. code-block:: php

    $connection->beginTransaction();

    try {
        // ..

        $connection-> commit();
    } catch (\Exception $e) {
        $connection->rollback();
    }

.. tip::

    You can use the ``inTransaction`` method to check if you're currently in a transaction.

Nested transactions
^^^^^^^^^^^^^^^^^^^

The Fridge DBAL supports nested transaction by using **savepoint**. A nested transaction looks as follows:

.. code-block:: php

    $connection->beginTransaction();

    try {
        // ..

        $connection->beginTransaction();

        try {
            // ..

            $connection-> commit();
        } catch (\Exception $e) {
            $connection->rollback();
        }

        // ..

        $connection-> commit();
    } catch (\Exception $e) {
        $connection->rollback();
    }

Transaction Isolation
^^^^^^^^^^^^^^^^^^^^^

The Fridge DBAL has methods to control the transaction isolation level as supported by the underlying database. The
supported isolation levels are represented by the following constants:

.. code-block:: php

    use Fridge\DBAL\Connection\Connection;

    Connection::TRANSACTION_READ_COMMITTED;
    Connection::TRANSACTION_READ_UNCOMMITTED;
    Connection::TRANSACTION_REPEATABLE_READ;
    Connection::TRANSACTION_SERIALIZABLE;

You can easily manipulate it with the following API:

.. code-block:: php

    $connection->setTransactionIsolation($transactionIsolation);

.. code-block:: php

    $transactionIsolation = $connection->getTransactionIsolation();

.. _Logger:           https://github.com/Seldaek/monolog
.. _Event Dispatcher: https://github.com/symfony/EventDispatcher
.. _SQL injection:    http://en.wikipedia.org/wiki/SQL_injection
.. _article:          http://en.wikipedia.org/wiki/Prepared_statement
