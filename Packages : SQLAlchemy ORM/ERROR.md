# ERROR MESSAGES

## 🎓 1. &nbsp; The connection between pymysql and sql server is frozen


### Describe the bug

**When using pymssql version 2.2.8 ~ 3.3.0 it freeze on connection, with no error, no timeout, just hang there forever.**


### To Reproduce

```Program Code
Database_connection = f"mssql+pymssql://{USERNAME}:{PASSWORD}@{SERVER}:{PORT}/{DATABASE}"
engine = create_engine(Database_connection)
Connection = engine.connect()
```


### Expected behavior

**$\color{orange}{\textsf{connect to sql server.}$**


### Current Behavior

**$\color{orange}{\textsf{freeze on connection line.}}$**


### Context (Environment)

* **OS : windows 10 64bit**
* **python : 3.8.9 - 3.12.3 (both)**
* **sqlalchemy : 2.0.3**
* **pymssql : 2.2.8 ~ 3.3.0 (both)**


### Additional context

**$\color{red}{\textsf{Try to uninstall pymssql version 2.2.8 or above and use version 2.2.7 and solve the problem.}}$**
