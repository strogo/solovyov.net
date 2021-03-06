tags: python, programming, sql, db
title:  SQLAlchemy: how to start
date: 2011-04-23
----

SQLAlchemy right now is an obvious leader of ORM solutions in Python,
but it has a problem: you will need to read quite a bit of documentation
to start using it. Which is why I've decided to write small introductory
tutorial.

Level 1: hand-written SQL
-------------------------

First step is obtaining a connection to a database:

    >>> from sqlalchemy import create_engine
    >>> e = create_engine('mysql://user:pass@host/db')
    >>> for r in e.execute('select * from table where id < %s', 2):
    ...     print dict(r)
    {u'id': 1, u'info': u'first row'}

If you want to use named parameters, there is `text()`:

```python
>>> from sqlalchemy import text
>>> result = e.execute(text('select * from table where id < :id'), {'id': 2})
```

From objects that you receive by iterating over result - `RowProxy` -
data can be obtained by index, by key or by attribute:

    >>> r[0] == r['id'] == r.id
        True

Transaction wanted?

```python
>>> c = e.connect()
>>> c.begin()
>>> # work work work
>>> c.commit() # try/except: c.rollback() optionally :)
```

That's already better than using raw DB API, especially given that
parameters are escaped.

Level 2: SQL-expressions in Python
----------------------------------

You can autoguess tables from database and then work with them, if
that's more suitable for you:

    >>> from sqlalchemy import Table, MetaData
    >>> meta = MetaData(bind=e, reflect=True)
    >>> table = meta.tables['table']
    >>> list(e.execute(table.select(table.c.id < 2)))
        [(1, u'first row')]

That was the same query, but using Python.

Level 3: ORM
------------

And if you prefer working with mapped objects, where you can add some
behavior:

    >>> from sqlalchemy import orm
    >>> class Table(object):
    ...     pass
    >>> orm.Mapper(Table, meta.tables['table'])
    >>> s = orm.Session(bind=e)
    >>> s.query(Table).filter(Table.id < 2).first().info
        u'first row'

In this case you can use full power of ORM:

    >>> class Artist(object):
    ...     pass
    >>> orm.Mapper(Artist, meta.tables['artist'])
    >>> class Album(object):
    ...     pass
    >>> orm.Mapper(Album, meta.tables['album'])
    >>> class Song(object):
    ...     pass
    >>> orm.Mapper(Song, meta.tables['song'])
    >>> s.query(Song).join(Album).filter(Album.id == 10).count()
        12L
    >>> # Song is given as first in this case, you'll need to join with Album
    >>> s.query(Song.name, Album.name).join(Album).join(Artist).filter(Artist.id == 2).first()
        (u'Hex', u'Inflikted')
    >>> print s.query(Song.name, Album.name).join(Album).join(Artist).filter(Artist.id == 2)
    SELECT song.name AS song_name, album.name AS album_name
    FROM song JOIN album ON album.id = song.album_id JOIN artist ON artist.id = album.artist_id
    WHERE artist.id = %(id_1)s

Also if you're going to use `Session.execute()`, it accepts strings with
named parameters by default:

    >>> list(s.execute('select * from table where id < :id', {'id': 2}))
        [(1, u'first row')]

Miscellaneous
-------------

Should be noted that by default `Engine` already has a pool of
connections, which is useful.

`MetaData` with reflection and early binding is not a preferred way to
work with SQLA, it's there mostly for ad-hoc scripts and working in
REPL. Usually you want to construct your tables/classes at first and
then bind `Engine` to `MetaData` somewhere later - when you are
configuring your application, for example (by doing `meta.bind = e`).

`Session` often is not used directly, especially in multi-threaded
application - there is [orm.scoped_session][1], which creates
thread-local session class.

That is basically all I wanted to tell you, for futher information there
is [documentation](http://www.sqlalchemy.org/docs/). :)

[1]: http://www.sqlalchemy.org/docs/orm/session.html?highlight=scoped_session#sqlalchemy.orm.scoped_session
