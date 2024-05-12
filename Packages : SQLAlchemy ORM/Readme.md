# [**SQLAlchemy 2.0 Documentation**](https://docs.sqlalchemy.org/en/20/index.html)   

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



## 📣 Checking the Installed SQLAlchemy Version

**This documentation covers SQLAlchemy version 2.0. If you’re working on a system that already has SQLAlchemy installed, check the version from your Python prompt like this:**

```sqlalchemy version
import sqlalchemy

sqlalchemy.__version__  
```


