### Installation  

- Gunicorn or uWsgi or mod_wsgi(for windows).  

- Database  
  - MySQL  
    - Storage engine
      - innoDB (default).  
      - MyISAM.  
        - Doesn't support transaction
        - Doesn't support foreign key

    - Drivers  
      - mysqlclient  
        `$ pip install mysqlclient`

    - [Create timezone definitions](
      https://dev.mysql.com/doc/refman/8.0/en/mysql-tzinfo-to-sql.html)

    - Creating database  
      `CREATE DATABASE <dbname> CHARACTER SET utf8;`  

- Install Django on virtualenv

