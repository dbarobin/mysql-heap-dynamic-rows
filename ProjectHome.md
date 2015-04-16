This project provides support for variable size records (aka dynamic row format) in MySQL Heap (Memory) Engine.

Contributed under GPL2 by eBay, Inc April 2008



As of March 2008, MySQL Heap Engine of any version is limited to fixed row format. It allocates fixed memory size for each record stored in a given Heap Table. For example, if table A has a VARCHAR(4000) column, MySQL will use at least 4000 bytes (plus other columns and per-record overhead) for _every_ record regardless of whether it has that much user data. In this example, the table will use 4GB memory per 1M records.

Assuming that actual data in a VARCHAR varies (average data length is less than maximum), it would be best if Heap Engine could optimize its memory use. In the example above, if average VARCHAR data was 1000 bytes, the memory consumption would be 1GB rather than 4GB.

This project changes memory allocation mechanisms used by the MySQL Heap Engine. Each Heap Table uses separate area of memory for its own private use. When inserting new records, previous versions of MySQL would always allocate areas of "reclength" size. Reclength equals to maximum space that could be ever needed by one record in a given table.

The new approach adds a "create table" option to set chunk sizes. When inserting a new record, Heap Engine will analyze actual record data and calculate the number of "chunks" needed for a compact form of that record. It will then allocate the necessary chunks, link them together into a chunkset and store data in these chunks.

Variable size records provide "dynamic" row format, as opposed to "fixed" row format. For backwards compatibility, Heap Tables default to fixed row format. The project's scope is Heap (aka Memory) Engine/Table only, MyISAM and other engines do not have similar limitations.

For added flexibility, project introduces new HP\_DATASPACE construct to MySQL Heap Engine, which should greatly simplify future BLOB support. Essentially, all BLOB's in a table would share their own HP\_DATASPACE instance. Unfortunately, the author of this project did not have time to add BLOB implementation.

All the interesting technical details are provided in a big comment section in the beginning of hp\_dspace.c, which documents design. The same information has been copied to the project's Wiki.

This project has been implemented by [Igor Chernyshev](http://www.linkedin.com/in/igorch) of eBay Kernel Team.


**UPDATE (July 30, 2008):** Larry Zhou from Google and author of this project have contributed a few bug fixes. The diff and zip files have been updated accordingly. Original diff can be found in "Deprecated" downloads.



Here is a list of related Web pages:

eBay Wins Application of the Year at MySQL Conference & Expo
(The patch you're looking at was one of the two patches, which were critical to the success of that project)
http://ebayinkblog.com/2008/04/15/ebay-wins-application-of-the-year-at-mysql-conference-expo and
http://en.oreilly.com/mysql2008/public/schedule/detail/1240

MySQL Internals thread where Sergei and I discussed some elements of the design:
http://lists.mysql.com/internals/35491

[Bug #25007](https://code.google.com/p/mysql-heap-dynamic-rows/issues/detail?id=25007) memory tables with dynamic rows format:
http://bugs.mysql.com/bug.php?id=25007

WL#1041: Blob & dynamic row format support for HEAP tables:
http://forge.mysql.com/worklog/task.php?id=1041

"MEMORY FORMAT=DYNAMIC" discussion:
http://forums.mysql.com/read.php?92,130771,130771#msg-130771

First blob implementation for heap engine from May 2007. That implementation uses my\_malloc for blobs, which cannot handle large number of rows and may cause massive memory fragmentation:
http://lists.mysql.com/internals/34486

"Varchar patch for MySQL memory engine", by MySQL employee, referring to this patch:
http://fallenpegasus.livejournal.com/707874.html