---
layout: post
title: "Setup and Run lobsters from Source Code"
categories: Linux Ruby Website 
---

Install Mariadb and set:
````bash
sudo apt install mariadb-server mariadb-client
sudo service maraidb start
# default: -h localhost -u root
mysql
````

Install Ruby
````bash
wget https://github.com/postmodern/ruby-install/releases/download/v0.9.3/ruby-install-0.9.3.tar.gz
tar xf ruby-install-0.9.3.tar.gz
cd ruby-install-0.9.3/bin
# find required ruby version in file: https://github.com/lobsters/lobsters/blob/master/.ruby-version
# this well take > 10 mins depending on your machine
./ruby-install 3.3.1
# add ruby bins to your PATH; write to profile files to persis
export PATH=/opt/rubies/ruby-3.3.1/bin:$PATH 
# refresh binary location
type ruby
````

Install Node.js:
````bash
sudo apt install nodejs
````

Setup lobsters:
````bash
git clone https://github.com/zhangysh1995/lobsters.git
cd lobsters
# check database info, and create the corresponding users
cat config/database.yml

# follow the project manual
./bin/setup
````
I *do recommend* to user a non-root normal user for you production environment, to avoid privilege problem and possible security vulnerable bugs for your database.

Start lobsters:
````bash
# production environment, default port is 3000
rails server -b 0.0.0.0 -p 80
````

To make user invitation to work at first step, we need a browser command line:
````bash
root@e9aae30ca121:/lobsters# sudo apt install w3m
root@e9aae30ca121:/lobsters# whereis w3m
w3m: /usr/bin/w3m /usr/lib/w3m /etc/w3m /usr/share/w3m
root@e9aae30ca121:/lobsters# export BROWSER=/usr/bin/w3m
````

