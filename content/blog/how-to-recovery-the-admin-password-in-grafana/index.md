---
title: Recovery admin password for grafana
comments: true
date: 2018-01-13
tags:
  - grafana
  - monitoring
---

For some reason, you lost your grafana admin password. So, if you've been using `sqlite` as your grafana backend storage, to recovery this password is easy. In order to do that, follow these steps:

1. In your local machine, install the sqlite3 package

```shell
sudo apt-get install sqlite3
```
 
2. Login int your sql database

```shell
sudo sqlite3 /var/lib/grafana/grafana.db
```
 
3. Reset the admin password using SQL update 

```sql
sqlite> update user set password = '59acf18b94d7eb0694c61e60ce44c110c7a683ac6a8f09580d626f90f4a242000746579358d77dd9e570e83fa24faa88a8a6', salt = 'F3FAxVm33R' where login = 'admin';
sqlite> .exit
```

4. Using the same string for the sql update command, now you should can to login using this credential: `username: admin` and `password: admin`
 

#### Note:

Of course, the final step will be change your grafana password, via its web interface

