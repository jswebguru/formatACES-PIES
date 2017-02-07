#aceslint
ACESlint is a simple command-line "Lint" tool for evaluating ACES xml (AAIA automotive catalog standard) files
The ACES standard is a product of autocare.org (http://autocare.org/technology/).
It is a way for automotive industry trading partners to share catalog (part-vehicle "fitment") in an organized way.

The ACES spec only defines a basic structure for communication "application" data between trading partners. AutoCare.org
provides an XSD schema file for validating the structural correctness of the ACES xml file. It does not have clearly defined
rules about the quality of the data conveyed. For example, a common quality problem is an "overlap".
This is where two different "App" nodes in the xml refer to the same basevehicle with different partnumbers. The real-world
consequence of this is that the end consumer (or counterman selling the part) is faced with two different parts for his/her
car and no clear understanding of which is the right choice.

This tool is completely contained in a single .c source file. It requires libxml2 to be present on the local system.
It can be compiled with MySQL support ("-DWITH_MYSQL" gcc command-line switch). If MySQL support is compiled-in, you
can specify a database to validate VCdb codes against. If an ACES xml file is linted without referencing a database,
only partial validation will be performed (duplicates, overlaps and CNC overlaps). If a vcdb database is provided, extended
validation is done on the file (basevehicle id validation, attribute code validation, and attribute combinations validation)

If you are interested in using/testing/contributing, feel free to contact Luke Smith lsmith@autopartsource.com. Our industry
can benefit from open-source tools and collaboration.



Compilation
----------------------------
with mysql support
gcc -o aceslint `xml2-config --cflags` aceslint.c `xml2-config --libs` -L/usr/lib/mysql -lmysqlclient -lz -DWITH_MYSQL


without mysql support - vcdb features will be disabled
gcc -o aceslint `xml2-config --cflags` aceslint.c `xml2-config --libs` -L/usr/lib/mysql -lmysqlclient -lz



Running
---------------------------

command-line switches:
-d  database name (example vcdb20161231)
-h  database host ("localhost" is assumed if switch is not used)
-u  database user ("" is assumed if switch is not used. This is valid for a typical MySQL installation where no permissions or users have been defined)
-p  database password ("" is assumed if switch is not used. This is valid for a typical MySQL installation where no permissions or users have been defined)
-v  verbosity level (0 is assumed if switch is not used. Level 0 will give only a high-level listing or audit results)

example 1 - simple database-less audit:
aceslint ACES_3_1_AirQualitee_FULL_2017-01-12.xml

example 2 - referencing a specific database for code validation:
aceslint ACES_3_1_AirQualitee_FULL_2017-01-12.xml -d vcdb20171231



database creation
--------------------------
VCDB_schema.sql contains the table creation SQL statements for making an empty VCdb on a MySQL server. 
load_VCDB_tables.sql contains the script that imports tab-delimited text data files into database structure. AutoCare.org offer 
"ascii text" as one of the download options to its subscribers.








