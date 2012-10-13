Query Builder
=============

The Fridge DBAL is shipped with a powerfull query builder. It provides an object-oriented way for writing an SQL
statement by allowing developers to use a `fluent interface`_ to specify individual parts of the SQL statement.

To get a ``Fridge\DBAL\Query\QueryBuilder``, you need to call the ``createQueryBuilder`` method on your connection.

.. code-block:: php

    $queryBuilder = $connection->createQueryBuilder();

.. note::

    Each time you call this method, you get a new query builder instance.

API
---

In order to build your SQL statement, the query builder offers a fluent API which allows to interact with all statement
aspects. You must know that each time you interact with any API method, the query builder will append datas to the
query builder.

Select
^^^^^^

The select method specifies the ``SELECT`` part of a query. It accepts a unique parameters which describes the columns
to be selected, which can be either a string representing comma-separated columns, or an array of column names.

.. code-block:: php

    $queryBuilder->select();

    /* SELECT * */

.. code-block:: php

    $queryBuilder->select('firstname, lastname');

    /* SELECT firstname, lastname */

.. code-block:: php

    $queryBuilder->select(array('firstname', 'lastname'));

    /* SELECT firstname, lastname */

From
^^^^

The from method specifies the ``FROM`` part of a query. It takes as first parameter the table name to be selected from.
It accepts as second parameter the table alias.

.. code-block:: php

    $queryBuilder
        ->select()
        ->from('user');

    /* SELECT * FROM user */

.. code-block:: php

    $queryBuilder
        ->select('u.lastname')
        ->from('user', 'u');

    /* SELECT u.lastname FROM user u */

Join
^^^^

The join method specifies the ``JOIN`` part of a query. It takes five parameters which describes respectively the from
alias, the join type, the join table name, the join table alias & the join condition.

.. code-block:: php

    $queryBuilder
        ->select()
        ->from('user', 'u')
        ->join('u', 'inner', 'group', 'g', 'g.id = u.group_id');

    /* SELECT * FROM user u INNER JOIN group g ON g.id = u.group _id */

The Fridge query builder proposes three common join methods: ``inner``, ``left`` & ``right`` join.

Inner Join
~~~~~~~~~~

The inner join method takes four parameters which describes respectively the from alias, the join table name, the join
table alias & the join condition.

.. code-block:: php

    $queryBuilder
        ->select()
        ->from('user', 'u')
        ->innerJoin('u', 'group', 'g', 'g.id = u.group_id');

    /* SELECT * FROM user u INNER JOIN group g ON g.id = u.group_id */

Left Join
~~~~~~~~~

The left join method takes four parameters which describes respectively the from alias, the join table name, the join
table alias & the join condition.

.. code-block:: php

    $queryBuilder
        ->select()
        ->from('user', 'u')
        ->leftJoin('u', 'group', 'g', 'g.id = u.group_id');

    /* SELECT * FROM user u LEFT JOIN group g ON g.id = u.group_id */

Right Join
~~~~~~~~~~

The right join method takes four parameters which describes respectively the from alias, the join table name, the join
table alias & the join condition.

.. code-block:: php

    $queryBuilder
        ->select()
        ->from('user', 'u')
        ->rightJoin('u', 'group', 'g', 'g.id = u.group_id');

    /* SELECT * FROM user u RIGHT JOIN group g ON g.id = u.group_id */

Where
^^^^^

The where method specifies the ``WHERE`` part of a query. It takes as first parameter the expression which can be either
a string representing the expression or an array of simple expression. It accepts as second parameter the expression
type (AND, OR).

.. code-block:: php

    $queryBuilder
        ->select()
        ->from('user')
        ->where('id = ?');

    /* SELECT * FROM user WHERE id = ? */

.. code-block:: php

    $queryBuilder
        ->select()
        ->from('user')
        ->where(array('id = ?', 'enabled = ?'), 'AND');

    /* SELECT * FROM user WHERE id = ? AND enabled = ? */

.. note::

    You can use the :doc:`Expression Builder<expression_builder>` to create your own expression and pass it to the
    ``where``, ``andWhere`` & ``orWhere`` methods.

And
~~~

The andWhere method appends an ``AND`` expression to the ``WHERE`` part of a query.

.. code-block:: php

    $queryBuilder
        ->select()
        ->from('user')
        ->where('enabled = ?')
        ->andWhere('createdAt > ?');

    /* SELECT * FROM user WHERE enabled = ? AND createdAt > ? */

Or
~~

The orWhere method appends an ``OR`` expression to the ``WHERE`` part of a query.

.. code-block:: php

    $queryBuilder
        ->select()
        ->from('user')
        ->where('enabled = ?')
        ->orWhere('createdAt < ?');

    /* SELECT * FROM user WHERE enabled = ? OR createdAt < ? */

Group By
^^^^^^^^

The groupBy method specifies the ``GROUP BY`` part of a query. It takes a first parameter which describes the columns
to be grouped by, which can be either a string representing comma-separated columns, or an array of columns.

.. code-block:: php

    $queryBuilder
        ->select(array('u.id', 'COUNT(p.id) AS picture_count'))
        ->from('user', 'u')
        ->leftJoin('u', 'picture', 'p', 'p.user_id = u.id')
        ->groupBy('u.id');

    /*
        SELECT u.id, COUNT(p.id) AS picture_count
        FROM user u
        LEFT JOIN picture p ON p.user_id = u.id
        GROUP BY u.id
    */

.. code-block:: php

    $queryBuilder
        ->select(array(
            'u.id',
            'COUNT(r.id) AS review_count',
            'COUNT(p.id) AS picture_count',
        ))
        ->from('user', 'u')
        ->leftJoin('u', 'picture', 'p', 'p.user_id = u.id')
        ->leftJoin('u', 'review', 'r', 'r.user_id = u.id')
        ->groupBy(array('u.id', 'r.id'));

    /*
        SELECT u.id, COUNT(r.id) AS review_count, COUNT(p.id) AS picture_count
        FROM user u
        LEFT JOIN picture p ON p.user_id = u.id
        LEFT JOIN review r ON r.user_id = r.id
        GROUP BY u.id, r.id
    */

Having
^^^^^^

The having method specifies the ``HAVING`` part of a query. It takes as first parameter the expression which can be
either a string representing the expression or an array of simple expression. It accepts as second parameter the
expression type (AND, OR).

.. code-block:: php

    $queryBuilder
        ->select(array('u.id', 'COUNT(p.id) AS picture_count'))
        ->from('user', 'u')
        ->leftJoin('u', 'picture', 'p', 'p.user_id = u.id')
        ->groupBy('u.id')
        ->having('picture_count > 0');

    /*
        SELECT u.id, COUNT(p.id) AS picture_count
        FROM user u
        LEFT JOIN picture p ON p.user_id = u.id
        GROUP BY u.id
        HAVING picture_count > 0
    */

.. code-block:: php

    $queryBuilder
        ->select(array(
            'u.id',
            'COUNT(r.id) AS review_count',
            'COUNT(p.id) AS picture_count',
        ))
        ->from('user', 'u')
        ->leftJoin('u', 'picture', 'p', 'p.user_id = u.id')
        ->leftJoin('u', 'review', 'r', 'r.user_id = u.id')
        ->groupBy(array('u.id', 'r.id'))
        ->having(array('review_count > 0', 'picture_count > 0'), 'AND');

    /*
        SELECT u.id, COUNT(r.id) AS review_count, COUNT(p.id) AS picture_count
        FROM user u
        LEFT JOIN picture p ON p.user_id = u.id
        LEFT JOIN review r ON r.user_id = r.id
        GROUP BY u.id, r.id
        HAVING review_count > 0 AND picture_count > 0
    */

.. note::

    You can use the :doc:`Expression Builder<expression_builder>` to create your own expression and pass it to the
    ``having``, ``andHaving`` & ``orHaving`` methods.

And
~~~

The andHaving method appends an ``AND`` expression to the ``HAVING`` part of a query.

.. code-block:: php

    $queryBuilder
        ->select(array(
            'u.id',
            'COUNT(r.id) AS review_count',
            'COUNT(p.id) AS picture_count',
        ))
        ->from('user', 'u')
        ->leftJoin('u', 'picture', 'p', 'p.user_id = u.id')
        ->leftJoin('u', 'review', 'r', 'r.user_id = u.id')
        ->groupBy(array('u.id', 'r.id'))
        ->having('review_count > 0')
        ->andHaving('picture_count > 0');

    /*
        SELECT u.id, COUNT(r.id) AS review_count, COUNT(p.id) AS picture_count
        FROM user u
        LEFT JOIN picture p ON p.user_id = u.id
        LEFT JOIN review r ON r.user_id = r.id
        GROUP BY u.id, r.id
        HAVING review_count > 0 AND picture_count > 0
    */

Or
~~

The orHaving method appends an ``AND`` expression to the ``HAVING`` part of a query.

.. code-block:: php

    $queryBuilder
        ->select(array(
            'u.id',
            'COUNT(r.id) AS review_count',
            'COUNT(p.id) AS picture_count',
        ))
        ->from('user', 'u')
        ->leftJoin('u', 'picture', 'p', 'p.user_id = u.id')
        ->leftJoin('u', 'review', 'r', 'r.user_id = u.id')
        ->groupBy(array('u.id', 'r.id'))
        ->having('review_count > 0')
        ->orHaving('picture_count > 0');

    /*
        SELECT u.id, COUNT(r.id) AS review_count, COUNT(p.id) AS picture_count
        FROM user u
        LEFT JOIN picture p ON p.user_id = u.id
        LEFT JOIN review r ON r.user_id = r.id
        GROUP BY u.id, r.id
        HAVING review_count > 0 OR picture_count > 0
    */

Order By
^^^^^^^^

The order method specifies the ``ORDER BY`` part of a query. It takes a first parameter which describes the columns to
be ordered by, which can be either a string representing comma-separated columns and order directions (ASC or DESC), or
an array of columns and order directions.

.. code-block:: php

    $queryBuilder
        ->select()
        ->from('user')
        ->orderBy('lastname ASC');

    /* SELECT * FROM user ORDER BY lastname ASC */

.. code-block:: php

    $queryBuilder
        ->select()
        ->from('user')
        ->orderBy(array('lastname ASC', 'firstname DESC'));

    /* SELECT * FROM user ORDER BY lastname ASC, firstname DESC */

Offset
^^^^^^

The offset method specifies the ``OFFSET`` part of a query.

.. code-block:: php

    $queryBuilder
        ->select()
        ->from('user')
        ->offset(10);

    /* SELECT * FROM user OFFSET 10 */

Limit
^^^^^

The limit method specifies the ``LIMIT`` part of a query.

.. code-block:: php

    $queryBuilder
        ->select()
        ->from('user')
        ->limit(10);

    /* SELECT * FROM user LIMIT 10 */

Insert
^^^^^^

The insert method allows to build an ``INSERT`` SQL statement which takes as first parameter the table name to be
inserted & as second parameter the table alias.

The set method allow to specify each column value to insert. It is strongly recommended to use the ``createPlaceholder``
method which will automatically generated a placeholder & bind your parameter on it.

.. code-block:: php

    $queryBuilder
        ->insert('user')
        ->set('firstname', $queryBuilder->createPlaceholder($firstname))
        ->set('lastname', $queryBuilder->createPlaceholder($lastname));

    /* INSERT INTO user (firstname, lastname) VALUES (?, ?) */

Update
^^^^^^

The update method allows to build an ``UPDATE`` SQL statement which takes as first parameter the table name to be
updated & as second parameter the table alias.

The set method allow to specify each column value to update. It is strongly recommended to use the ``createPlaceholder``
method which will automatically generated a placeholder & bind your parameter on it.

.. code-block:: php

    $queryBuilder
        ->update('user')
        ->set('enabled', $queryBuilder->createPlaceholder($enabled));

    /* UPDATE user SET enabled = ? */

Delete
^^^^^^

The delete method allows to build a ``DELETE`` SQL statement which takes as first parameter the table name to be
deleted & as second parameter the table alias.

.. code-block:: php

    $queryBuilder->delete('user');

    /* DELETE FROM user */

Parameters
----------

To avoid :doc:`security issues<security>`, the query builder supports prepared statement & so, positional and named
placeholders. Like for a prepared statement, it is not possible to mix them together.

.. code-block:: php

    $queryBuilder
        ->setParameter(1, $firstname, \PDO::PARAM_STR)
        ->setParameter(2, $lastname, 'string)
        ->setParameter(3, $enabled);

.. code-block:: php

    $queryBuilder
        ->setParameter('firstname', $firstname, \PDO::PARAM_STR)
        ->setParameter('lastname', $lastname, 'string)
        ->setParameter('enabled', $enabled);

.. code-block:: php

    $queryBuilder->setParameters(
        array($firstname, $lastname, $enabled),
        array(\PDO::PARAM_STR, 'string')
    );

    // OR

    $queryBuilder->setParameters(
        array('firstname' => $firstname, 'lastname'  => $lastname, 'enabled' => $enabled),
        array('firstname' => \PDO::PARAM_STR, 'lastname' => 'string')
    );

Generate & Bind Parameter
-------------------------

The Fridge query builder allows developers to generate a placeholder & bind a value on it in a single call. You can
either generate a positional or a named placeholder or even better let the query builder decides which is appropriate.
Each methods can take as second parameter any ``PDO::PARAM_*`` type or any :doc:`Fridge DBAL type<type>`.

.. code-block:: php

    $placeholder = $queryBuilder->createPositionalPlaceholder($value);

    /* $placeholder = '?' */

.. code-block:: php

    $placeholder = $queryBuilder->createNamedPlaceholder($value);

    /* $placeholder = ':fridge1' */

.. code-block:: php

    $placeholder = $queryBuilder->createPlaceholder($value);

    /* $placeholder = '?' */
    // OR
    /* $placeholder = ':fridge1' */

Get Query
---------

To get your query, you just need to call the ``getQuery`` method.

.. code-block:: php

    $query = $queryBuilder->getQuery();

Execute Query
-------------

To execute your query, you just need to call the ``execute`` method.

.. code-block:: php

    $statement = $queryBuilder->execute();

Reset Builder
-------------

The query builder is composed of parts, so, if you want to reset your query builder, you need to reset all of them:

.. code-block:: php

    $queryBuilder->resetParts();

A query builder is composed of the following parts:

* select
* from
* join
* set
* where
* group_by
* having
* order_by
* offset
* limit

If you want to reset a single part, you can call the ``resetPart`` function and specify the part you want to reset.

.. code-block:: php

    $queryBuilder->resetPart('select');

If you want to reset multiple specific parts, you can call the ``resetParts`` function and specify parts you want to
reset.

.. code-block:: php

    $queryBuilder->resetParts(array('offset', 'limit'));

.. _fluent interface: http://en.wikipedia.org/wiki/Fluent_interface
