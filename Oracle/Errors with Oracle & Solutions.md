## ORA-12505: TNS:Listener does not currently know of SID given in connect descriptor

### Investigation

Connect to sqlplus to check status instance

```sh
sqlplus / as sysdba
```

You will see the result likes as: 

​	Note the line : **Connected to an idle instance**

```sh
SQL*Plus: Release 19.0.0.0.0 - Production on Mon Dec 21 17:23:20 2020
Version 19.3.0.0.0

Copyright (c) 1982, 2019, Oracle.  All rights reserved.

Connected to an idle instance.
```

### Resolution

```
sqlplus / as sysdba
startup
```



## ORA-12541 TNS: no listener

### Investigation

Check listener.ora **($ORACLE_HOME/network/admin/listener.ora)**

Result:

```sql
LISTENER =
  (DESCRIPTION_LIST =
    (DESCRIPTION =
      (ADDRESS = (PROTOCOL = IPC)(KEY = EXTPROC1521))
      (ADDRESS = (PROTOCOL = TCP)(HOST = 0.0.0.0)(PORT = 1521))
    )
  )
```

Check listener status

```shell
lsnrctl status
```

Notes: **Error message will shown in result.**

### Solution

restart listener

```shell
lsnrctl start
```

Check listener again

```shell
lsnrctl status
```

