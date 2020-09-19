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

````
$ git clone https://github.com/mysql/mysql-server \
                      --single-branch branch mysql-cluster-8.0.21
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
````
$ mkdir bld
$ cd bld
$ cmake .. -DDOWNLOAD_BOOST=1 -DWITH_BOOST=/any_path_you_like
$ make -j10 # thread you want to use
$ make install
````

## Set User mysql
For the detail, see [Create a mysql User and Group](https://dev.mysql.com/doc/refman/8.0/en/binary-installation.html#binary-installation-createsysuser).
````
$ groupadd mysql
$ useradd -r -g mysql -s /bin/false mysql
````

## Set Data Directory
````
$ # Postinstallation setup
$ cd /usr/local/mysql
$ mkdir mysql-files
$ chown mysql:mysql mysql-files
$ chmod 750 mysql-files
````

## Initialize & Run
````
$ ./bin/mysqld --initialize --user=mysql
$ ./bin/mysql_ssl_rsa_setup
$ ./bin/mysqld_safe --user=mysql &
````
See [Troubleshooting Problems Starting the MySQL Server](https://dev.mysql.com/doc/refman/8.0/en/starting-server-troubleshooting.html) if you cannot get the server start.
