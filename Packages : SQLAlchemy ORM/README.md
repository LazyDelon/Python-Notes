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


## 🎓 Declare Models

**Here, we define module-level constructs that will form the structures which we will be querying from the database. This structure, known as a Declarative Mapping, defines at once both a Python object model, as well as database metadata that describes real SQL tables that exist, or will exist, in a particular database:**

$\color{red}{\textsf{這裡的 Modals 也可以使用 flask 的寫法，這邊主要看你的 codeing 習慣，sqlalchemy 無論你用哪種方式都可以執行。}}$

```Models
from typing import List
from typing import Optional
from sqlalchemy import ForeignKey
from sqlalchemy import String
from sqlalchemy.orm import DeclarativeBase
from sqlalchemy.orm import Mapped
from sqlalchemy.orm import mapped_column
from sqlalchemy.orm import relationship

class Base(DeclarativeBase):
    pass

class User(Base):
    __tablename__ = "user_account"
    id: Mapped[int] = mapped_column(primary_key=True)
    name: Mapped[str] = mapped_column(String(30))
    fullname: Mapped[Optional[str]]
    addresses: Mapped[List["Address"]] = relationship(
        back_populates="user", cascade="all, delete-orphan"
    )
    def __repr__(self) -> str:
        return f"User(id={self.id!r}, name={self.name!r}, fullname={self.fullname!r})"

class Address(Base):
    __tablename__ = "address"
    id: Mapped[int] = mapped_column(primary_key=True)
    email_address: Mapped[str]
    user_id: Mapped[int] = mapped_column(ForeignKey("user_account.id"))
    user: Mapped["User"] = relationship(back_populates="addresses")
    def __repr__(self) -> str:
        return f"Address(id={self.id!r}, email_address={self.email_address!r})"
```



## 🎓 Session API

### Session and sessionmaker()

#### 🎓 Code A
| **Object Name**  |	**Description** |
| ---- | ---- |
| **ORMExecuteState**	| **Represents a call to the [**Session.execute()**](https://docs.sqlalchemy.org/en/20/orm/session_api.html#sqlalchemy.orm.Session.execute) method, as passed to the [**SessionEvents.do_orm_execute()**](https://docs.sqlalchemy.org/en/20/orm/session_api.html#sqlalchemy.orm.Session.execute)event hook.** |
| **Session**	| **Manages persistence operations for ORM-mapped objects.** |
| **sessionmaker**	| **A configurable [**Session**](https://docs.sqlalchemy.org/en/20/orm/session_api.html#sqlalchemy.orm.Session) factory.** |
| **SessionTransaction**	| **A Session-level transaction.** |
| **SessionTransactionOrigin**	| **indicates the origin of a [**SessionTransaction**](https://docs.sqlalchemy.org/en/20/orm/session_api.html#sqlalchemy.orm.SessionTransaction).** |



## 🎓 Example

```Example
from sqlalchemy import String
from sqlalchemy import VARCHAR
from sqlalchemy import Column
from sqlalchemy import Integer
from sqlalchemy import DECIMAL
from sqlalchemy import DateTime

from sqlalchemy import create_engine

from sqlalchemy.orm import sessionmaker
from sqlalchemy.orm import scoped_session
from sqlalchemy.ext.declarative import declarative_base


Database_Connection = "mssql+pymssql://thiner:v7928Tyh@10.27.1.61:6767/KacqDB?charset=utf8"

engine = create_engine(Database_Connection)

Session = sessionmaker(bind=engine)

con = engine.connect()

db = Session()

Base = declarative_base()



"""----- Models -----"""

class Test(Base):
    
    __tablename__ = '資料表名稱'

    Data_ID=Column(Integer, primary_key=True, autoincrement=True)
    pdt_time=Column(DateTime, nullable =False)         
    pdt_type=Column(VARCHAR(30), nullable =False)  
    No=Column(Integer, nullable =False)
    T1=Column(DECIMAL(4,2), nullable =False)
    T2=Column(DECIMAL(4,2), nullable =False)  
    T3=Column(DECIMAL(4,2), nullable =False)

    def __str__(self):
        return f"Test('{self.pdt_time}', '{self.pdt_type}', '{self.No}', '{self.T1}', '{self.T2}', '{self.T3}')"

    def __init__(self, pdt_time, pdt_type, No, T1, T2, T3):

        self.pdt_time = pdt_time 
        self.pdt_type = pdt_type
        self.No = No
        self.T1 = T1
        self.T2 = T2
        self.T3 = T3



"""----- Insert -----"""

def insert(pdt_time, pdt_type, No, T1 ,T2 ,T3):
    
    data = Test(pdt_time, dt_type, No, T1, T2, T3)

    db.add(data)
    
    db.commit()



if __name__ == '__main__':

    insert("2024-05-08", "xx", "xx", "xx", "xx", "xx")
    
```
