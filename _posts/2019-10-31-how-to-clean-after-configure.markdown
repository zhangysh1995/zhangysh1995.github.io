---
layout: post
title: "Clean Configure Generated Files"
categories: notes gnu tool 
---

## Normal step


At least you need to do `make distclean` instead of `make clean`. But in some ca
se, you may want to have a fresh source to compile from scratch.

````
make mostlyclean
make clean
make distclean
make maintainer-clean
````


## Explanation
*  [Usage](https://stackoverflow.com/questions/2936116/autotools-how-to-cleanup-files-created-by-configure-in-lighttpd-project)
*  [What are the commands?](https://www.gnu.org/savannah-checkouts/gnu/automake/manual/html_node/Clean.html#Clean)

