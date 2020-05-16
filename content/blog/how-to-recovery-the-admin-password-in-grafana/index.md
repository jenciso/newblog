---
title: Recover your grafana admin password using sqlite shell
comments: true
date: 2018-01-13
tags:
  - grafana
  - monitoring
---

One day you can lose your grafana admin password and probably you will desire to recover it rather than reinstall. If your grafana has been using `sqlite` as backend storage, the recover process is easy. Here are the steps to do that:

1. In your local machine, install the sqlite3 package

```shell
sudo apt-get install sqlite3
```
 
2. Login into your sql database

```shell
sudo sqlite3 /var/lib/grafana/grafana.db
```
 
3. Reset the admin password using SQL update (the new password will be `admin`)

```sql
sqlite> update user set password = '59acf18b94d7eb0694c61e60ce44c110c7a683ac6a8f09580d626f90f4a242000746579358d77dd9e570e83fa24faa88a8a6', salt = 'F3FAxVm33R' where login = 'admin';
sqlite> .exit
```

Now, you could login in your grafana web interface using `username: admin` and `password: admin`
 
