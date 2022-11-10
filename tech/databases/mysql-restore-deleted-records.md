---
title: How to restore deleted records in MySQL
date: 2021-08-12

tags:
  - mysql
  - tech notes

---
# How to restore deleted records in MySQL

> This assumes you have a disk backup containing the records you accidentally deleted!

- Create a new server mounting the old disk
- Export the old table
```sh
mysqldump -u <user> -p <database> <table> --where="<condition>" --no-create-info > records_to_restore.sql
```

- Check the sql contains the inserts you want
  - in particular, that it does NOT contain DROP/CREATE tables statements. That's what the `no-create-info` is for.
- Copy the file to the original server
- Import the records into the original database
```sh
mysql -u <user> -p <database> < records_to_restore.sql
```
