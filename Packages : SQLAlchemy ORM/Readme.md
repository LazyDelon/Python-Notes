# [**SQLAlchemy 2.0 Documentation**](https://docs.sqlalchemy.org/en/20/index.html)   

==========

|PyPI| |Python| |Downloads|

.. |PyPI| image:: https://img.shields.io/pypi/v/sqlalchemy
    :target: https://pypi.org/project/sqlalchemy
    :alt: PyPI

.. |Python| image:: https://img.shields.io/pypi/pyversions/sqlalchemy
    :target: https://pypi.org/project/sqlalchemy
    :alt: PyPI - Python Version

.. |Downloads| image:: https://static.pepy.tech/badge/sqlalchemy/month
    :target: https://pepy.tech/project/sqlalchemy
    :alt: PyPI - Downloads


The Python SQL Toolkit and Object Relational Mapper

Introduction
-------------

## 📋 Overview

**The SQLAlchemy SQL Toolkit and Object Relational Mapper is a comprehensive set of tools for working with databases and Python. It has several distinct areas of functionality which can be used individually or combined together. Its major components are illustrated below, with component dependencies organized into layers：**

&nbsp; <img src="./Images/Object Relational Mapper (ORM) and the Core.png" alt="ORM and Core"/>


**The SQL Expression Language is a toolkit on its own, independent of the ORM package, which provides a system of constructing SQL expressions represented by composable objects, which can then be “executed” against a target database within the scope of a specific transaction, returning a result set. Inserts, updates and deletes (i.e. DML) are achieved by passing SQL expression objects representing these statements along with dictionaries that represent parameters to be used with each statement.**

**The ORM builds upon Core to provide a means of working with a domain object model mapped to a database schema. When using the ORM, SQL statements are constructed in mostly the same way as when using Core, however the task of DML, which here refers to the persistence of business objects in a database, is automated using a pattern called unit of work, which translates changes in state against mutable objects into INSERT, UPDATE and DELETE constructs which are then invoked in terms of those objects. SELECT statements are also augmented by ORM-specific automations and object-centric querying capabilities.**



## 📣 Installation Guide

### Supported Platforms

#### SQLAlchemy supports the following platforms:

➤ &nbsp; cPython 3.7 and higher**

➤ &nbsp; Python-3 compatible versions of PyPy**

$\color{red}{\textsf{Changed in version 2.0: SQLAlchemy now targets Python 3.7 and above.}}$



## 🎓 Checking the Installed SQLAlchemy Version

**This documentation covers SQLAlchemy version 2.0. If you’re working on a system that already has SQLAlchemy installed, check the version from your Python prompt like this:**

```sqlalchemy version
import sqlalchemy

sqlalchemy.__version__  
```



## 🎓 Establishing Connectivity - the Engine

**The start of any SQLAlchemy application is an object called the Engine. This object acts as a central source of connections to a particular database, providing both a factory as well as a holding space called a connection pool for these database connections. The engine is typically a global object created just once for a particular database server, and is configured using a URL string which will describe how it should connect to the database host or backend.**

**For this tutorial we will use an in-memory-only SQLite database. This is an easy way to test things without needing to have an actual pre-existing database set up. The Engine is created by using the create_engine() function:**

```sqlalchemy version
from sqlalchemy import create_engine

engine = create_engine("sqlite+pysqlite:///:memory:", echo=True)
```




## 🎓 Working with Data

**In Working with Transactions and the DBAPI, we learned the basics of how to interact with the Python DBAPI and its transactional state. Then, in Working with Database Metadata, we learned how to represent database tables, columns, and constraints within SQLAlchemy using the MetaData and related objects. In this section we will combine both concepts above to create, select and manipulate data within a relational database. Our interaction with the database is always in terms of a transaction, even if we’ve set our database driver to use autocommit behind the scenes.**

**The components of this section are as follows:**

**➤ &nbsp; Using INSERT Statements - to get some data into the database, we introduce and demonstrate the Core Insert construct. INSERTs from an ORM perspective are described in the next section Data Manipulation with the ORM.**

**➤ &nbsp; Using SELECT Statements - this section will describe in detail the Select construct, which is the most commonly used object in SQLAlchemy. The Select construct emits SELECT statements for both Core and ORM centric applications and both use cases will be described here. Additional ORM use cases are also noted in the later section Using Relationships in Queries as well as the ORM Querying Guide.**

**➤ &nbsp; Using UPDATE and DELETE Statements - Rounding out the INSERT and SELECTion of data, this section will describe from a Core perspective the use of the Update and Delete constructs. ORM-specific UPDATE and DELETE is similarly described in the Data Manipulation with the ORM section.**



## 🎓 Inserting Rows using the ORM Unit of Work pattern

**When using the ORM, the Session object is responsible for constructing Insert constructs and emitting them as INSERT statements within the ongoing transaction. The way we instruct the Session to do so is by adding object entries to it; the Session then makes sure these new entries will be emitted to the database when they are needed, using a process known as a flush. The overall process used by the Session to persist objects is known as the unit of work pattern.**

### Instances of Classes represent Rows

```Data
>>> squidward = User(name="squidward", fullname="Squidward Tentacles")
>>> krabs = User(name="ehkrabs", fullname="Eugene H. Krabs")
```

### Adding objects to a Session

**To illustrate the addition process step by step, we will create a Session without using a context manager (and hence we must make sure we close it later!):**

```Session
session = Session(engine)
```

```Session.add
session.add(squidward)
session.add(krabs)
```


## 🎓 Committing

**There’s much more to say about how the Session works which will be discussed further. For now we will commit the transaction so that we can build up knowledge on how to SELECT rows before examining more ORM behaviors and features:**

```Session.commit
session.commit()
```


