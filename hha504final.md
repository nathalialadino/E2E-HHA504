# -*- coding: utf-8 -*-
"""
Spyder Editor

This is a temporary script file.
"""


#  Connecting to the Sever 
#### Enter the following to connect: Using Server name, IP Address, and Password

 ssh Nathalia@13.68.191.81
###### Enter Password: (on word doc)
###### Enter the following commands after each loads...
  sudo apt-get update
  sudo apt install mysql-client mysql-server
  sudo mysql
  
#### Creating a mysql user and its password

mysql> CREATE USER 'dba'@'%'IDENTIFIED BY'ahi2021';

mysql> select user from mysql.user;

mysql> GRANT ALL PRIVILEGES ON *.* TO 'dba'@'%'WITH GRANT OPTION;

mysql> exit()

#### Using the user and password made, connect to mysql db

Nathalia@AHIserver:~$ my sql -u dba -p
Enter password: ahi2021

#### View databases within the mysql instance

mysql> show databases;
mysql> CREATE DATABASE e2e;

#### Loading CSV data and Creating Table on Spyder - Python Script

from sqlalchemy import create_engine
import sqlalchemy

import pandas as pd

MYSQL_HOSTNAME = '13.68.191.81'
MYSQL_USER = 'dba'
MYSQL_PASSWORD = 'ahi2021'
MYSQL_DATABASE = 'e2e'

connection_string = f'mysql+pymysql://{MYSQL_USER}:{MYSQL_PASSWORD}@{MYSQL_HOSTNAME}/{MYSQL_DATABASE}'
engine = create_engine(connection_string)

print (engine.table_names())


###### connection refused - update mysql configuration settings with following commands:
Nathalia@AHIserver:~$ sudo nano /etc/mysql/mysql.conf.d/mysqld.cnf
###### edit bind-address to 0.0.0.0
###### restart
Nathalia@AHIserver:~$ sudo service mysql restart 

###### Try connecting again
##### Load csv file

csvfile = pd.read_csv(r'C:/Users/natha/OneDrive/Desktop/fludata/H1N1_Flu_Vaccines.csv')
csvfile.to_sql('H1N1_Flu_Vaccines', con=engine, if_exists='append')


#### Set up new connection on mysql workbench 
##### connection string: dba@13.68.191.81:3306

#### Rename table within mysql workbench
ALTER TABLE 'e2e'.'H1N1_Flu_Vaccinces'
RENAME TO 'e2e'.'H1N1';

###### Go back to mysql
##### View databases
mysql> show databases;
##### Use e2e db
mysql> use e2e;
##### Database changed, view tables within db (should see H1N1 table)
mysql> show tables;

### Creating a dump (.sql) file
mysql> exit
Nathalia@AHIserver:~$ mysqldump

Above command ensures that we have already properly installed mysqldump through the sql server

# Create dump file
Nathalia@AHIserver:~$ mysqldump -u dba -p e2e H1N1 > backup_H1N1.sql
enter password: ahi2021
Nathalia@AHIserver:~$ ls -1

Above command shows that the backup file has been created

Nathalia@AHIserver:~$ nano backup_H1N1.sql

### Restore
Nathalia@AHIserver:~$ mysql -u dba -p e2e < backup_H1N1.sql

### locate dump file path

Nathalia@AHIserver:~$ sudo apt install mlocate
Nathalia@AHIserver:~$ locate backup_H1N1.sql
/home/Nathalia/backup_H1N1.sql

# Using scp command to move the file into local computer

scp Nathalia@13.68.191.81:/home/Nathalia/backup_H1N1.sql /home/Nathalia/Desktop


# Create trigger on mysql workbench

USE e2e

delimiter $$
CREATE TRIGGER h1n1_concern_trigger BEFORE INSERT on H1N1 
FOR EACH ROW
BEGIN
	IF NEW.h1n1_concern >= 3 THEN
		SIGNAL SQLSTATE '45000'
		SET MESSAGE_TEXT = 'H1N1 concern should be a numerical value between 0 and 3. Please try again.';
    END IF;
END; $$
delimiter ;

show triggers;












