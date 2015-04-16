# Notes On The Patch Format #

Attached ZIP (see [Downloads](http://code.google.com/p/mysql-heap-dynamic-rows/downloads/list) section) contains all modified source files and a diff - all based on Unix 5.0.45 version of MySQL source code. DIFF was made using GNU DIFF (with -upraN options). The patch command should work like "patch -p1 -u -r reject\_file <diff.txt". I could not find free bitkeeper for Windows or Solaris. However, with complete source files you should be able to run BitKeeper? DIFF if necessary.

Before reading DIFF, please make sure to read comments in the beginning of hp\_dspace.c or in DesignDetails. It contains design description, as well as a list of open issues.

You may also want to look at the [MySQL Internals Thread](http://lists.mysql.com/internals/35491) where Sergei and I discussed some elements of the design.