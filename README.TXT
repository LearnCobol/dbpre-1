README.TXT for dbpre V 0.3                                           2014-06-03
--------------------------

Developed by the_piper@web.de
For more infos see here: https://sourceforge.net/projects/dbpre/

File List
=========
c                  - script to compile dbpre using gcc
cobmysqlapi.c      - the modified api for mysql from the OpenCobol forums
cobmysqlapi.o      - the object file of the api, to be linked to the programs
cPCTB001B          - compile script to compile test program 1
cPCTB002B          - compile script to compile test program 2
cPCTB002B          - compile script to compile test program 3
dbpre.c            - the source code of dbpre       
PCTB003B.param     - parameter file for test program 3 (cursors)
PCTB003B.scb       - source code of test program 3 (.scb = Sql CoBol)
PCTB003B.sh        - shell script to run test program 3
PGCTBBAT           - needed copybook, which includes some COBOL coding
PGCTBBATWS         - working storage stuff for copybook
SQLCA              - SQL communication area


Requirements
============
gcc
MySql
OpenCobol/GnuCobol


A short description how to use this early pre-alpha version of dbpre
====================================================================

The MySql API
-------------
If you can't use the object file of cobmysqlapi, which is included in this
release, you must compile your own one from cobmysqlapi.c.

This object file will be linked later to the COBOL-programs.

Compile it with: gcc -I/usr/include/mysql -c cobmysqlapi.c


Compiling dbpre
---------------
Use the shell script "c" to compile dbpre.c. It's just a simple C program,
which requires nothing unusual.

To test dbpre type: dbpre --help
This displays a help message and some more informations.


The framework PGCTB
-------------------
PGCTB is an abbreviation for Pipers Gnu Cobol Tool Box.

The current framework is a framework for batch programs. It consists
of the two files PGCTBBAT, which has COBOL commands and calls to the
MySql API, and PGCTBBATWS, the needed WORKING STORAGE definitions for
the framework.

Further the SQLCA, the SQL Communication Area used to communicate with the
MySql API.

This is a frame work for Batch programs (program name ends with the letter 'B').
Later i am going to add stuff for subroutines and transactions, which will then
have different copybooks.


The example programs
--------------------
There is one example programs coming with this release, PCTB003B.scb.

To compile it, use the appropriate script, cPCTB003B.

This is how compiling works:
First call dbpre with the program name without suffix
E.g: dbpre PCTB003B -I./ -ts=3

dbpre expects that the suffix of the source code is .scb (Sql CoBol).
dbpre will then produce a .cob and a .lst file.
The .lst file contains, like the .cob file, the COBOL program but also error 
messages, if dbpre has found any errors.

Load the .lst file with your favorite editor and search for ERROR:

If dbpre has reported no errors, the cobol source code, the .cob file, can
be compiled with the OpenCobol compiler.

E.g: cobc -x PCTB003B.cob cobmysqlapi.o -L/usr/lib/mysql -lmysqlclient

If cobc has no errors to report, you should now have an executable file
named PCTB003B.


The parameter file
------------------
The used framework, PGCTBBAT, uses a parameter file named <programname>.param,
e.g: PCTB003B.param, which has definitions needed to connect to the database.

Like this:

------(snip)-----------
DBHOST=localhost
DBUSER=root
DBPASSWD=YourPasswordHere           <=== change it!!
DBNAME=testdb
DBPORT=03306
DBSOCKET=null
------(snap)-----------

Before you can run the compiled test program, you must modify the param file
to your needs, specifying your password, your user name, your host for
mysql and so on.

When you have done that, you can start the test program by typing it's name
like PCTB003B and you should see the output.
You can use too the shell scripts, which are included for each test program,
but they only redirect the output into a log file, thats it.


The MySql example_table
------------------------
To use this sample program, you have to create a table in your MySql database,
which must look like this:

Name: example_table:

+--------+----------+------+-----+---------+-------+
| Field  | Type     | Null | Key | Default | Extra |
+--------+----------+------+-----+---------+-------+
| field1 | char(20) | NO   |     | NULL    |       |
| field2 | char(16) | NO   |     | NULL    |       |
| field3 | char(32) | NO   |     | NULL    |       |
+--------+----------+------+-----+---------+-------+
3 rows in set (0.00 sec)


The program structure
---------------------
So, as we have seen in the text above, a program, which can be precompiled
with dbpre consists of:

The source code with the EXEC SQL statements, the *.scb file

The .cob file, produced by dbpre, the input for cobc

The .lst file, produced by dbpre, which contains ERROR: messages, if there
are any

The copybooks of the framework, PGCTBBAT, PGCTBBATWS, SQLCA

The param file, which has the parameters to connect to the MySql server


Error handling
==============
Look at the example programs, how error handling is done with an
EVALUATE..END-EVALUATE statement.

To handle run time errors, the error message will tell you a sequence number.
Look at the .cob file and search for it to identify the SQL statement, which
caused the error.

For example, the error message reports a sequence number of 12, look at the
.cob file and search for "MOVE 12" until you have found the line
MOVE 12 TO SQLCA-SEQUENCE
and below that is the SQL-statement, which caused the error.


The End
=======
I hope, all this makes at least sense and is working, so you can test dbpre
a little bit.

For any comments, bug reports, suggestions, whatever, please post in the
discussion section at https://sourceforge.net/projects/dbpre/
or send an email to the_piper@web.de
