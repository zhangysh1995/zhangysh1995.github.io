---
layout: post
title: How to Install MySQL From Source
categories: notes mysql software
---

## Prerequisite
*  The version of MySQL you want to build. Find it in the [repository](https://github.com/mysql/mysql-server).
*  The build tools
    - `apt install build-essential -y`
    - `apt install cmake -y`
*  Use `git` to clone the source code
    - `apt install git -y`
*  Use `mysql` to connect to the server
    - `apt install mysql-client -y`

## Download the source code
I want to build the latest version `MySQL 8.0.21`, then run:

```` bash
$ git clone --branch mysql-cluster-8.0.21 \
            https://github.com/mysql/mysql-server                      
$ cd mysql-server                   
````

I only need this version so I clone a single branch.

## Install Dependencies
For a full description, please refer to [Source Installation Prerequisites](https://dev.mysql.com/doc/refman/8.0/en/source-installation-prerequisites.html).

To install all dependecies on `Ubuntu`, run:

````
apt update && \
    apt install libssl-dev libncurses5-dev pkg-config bison -y
````

### boost
Will be installed later with the `CMake` command.

### openssl
`apt get install libssl-dev`

### curses
`apt install libncurses5-dev`

### pkg-config
`apt install pkg-config`

### bison
`apt install bison`

## Build & Install
```` bash
$ mkdir bld
$ cd bld
$ cmake .. -DDOWNLOAD_BOOST=1 -DWITH_BOOST=/any_path_you_like
$ make -j10 # thread you want to use
$ make install
````

## Set User mysql
For the detail, see [Create a mysql User and Group](https://dev.mysql.com/doc/refman/8.0/en/binary-installation.html#binary-installation-createsysuser).
```` bash
$ groupadd mysql
$ useradd -r -g mysql -s /bin/false mysql
````

## Set Data Directory
```` bash
$ # Postinstallation setup
$ cd /usr/local/mysql
$ mkdir mysql-files
$ chown mysql:mysql mysql-files
$ chmod 750 mysql-files
````

## Initialize & Run
```` bash
$ ./bin/mysqld --initialize --user=mysql \
      # this step will generate a random password and print it out
$ ./bin/mysql_ssl_rsa_setup
$ ./bin/mysqld_safe --user=mysql &
````
See [Troubleshooting Problems Starting the MySQL Server](https://dev.mysql.com/doc/refman/8.0/en/starting-server-troubleshooting.html) if you cannot get the server start.

## A Possible Issue with Connection
I can get the server run without any error:

```` bash
root@39d4ee9ee7de:/usr/local/mysql# ps aux              
USER         PID %CPU %MEM    VSZ   RSS TTY      STAT START   TIME COMMAND
root           1  0.0  0.0  34892 19808 pts/0    Ss   09:05   0:00 /bin/bash
root       47607  0.0  0.0   4628  1700 pts/0    T    11:14   0:00 /bin/sh ./bin/mysqld_safe --user=mysql --verbose
mysql      47693  0.4  0.1 2610440 289408 pts/0  Sl   11:14   0:02 /usr/local/mysql/bin/mysqld --basedir=/usr/local/mysql --dat
root       47868  0.0  0.0  34404  2860 pts/0    R+   11:23   0:00 ps aux
````

However, I cannot connect to the server via `mysql` client:

```` bash
root@39d4ee9ee7de:/usr/local/mysql# mysql
ERROR 2002 (HY000): Can't connect to local MySQL server through socket '/var/run/mysqld/mysqld.sock' (2)
````

After checking [Troubleshooting Problems Connecting to MySQL](https://dev.mysql.com/doc/refman/8.0/en/problems-connecting.html), I found that I have try to connect to the wrong socket:

```` bash
root@39d4ee9ee7de:/usr/local/mysql# netstat -ln | grep mysql
unix  2      [ ACC ]     STREAM     LISTENING     563859719 /tmp/mysql.sock
unix  2      [ ACC ]     STREAM     LISTENING     563864785 /tmp/mysqlx.sock
````

Try connecting with the correct socket:

```` bash
root@39d4ee9ee7de:/usr/local/mysql# mysql --socket /tmp/mysql.sock -p
Enter password:
````

Hooray! It works now!!!
