ACESlint is a simple command-line "Lint" tool for evaluating ACES xml (Automotive Catalog Enhanced Standard) files.
ACES is a product of autocare.org (http://autocare.org/technology/). It is a way for automotive industry trading partners to 
share catalog (part-vehicle "fitment") in an organized way.

 The ACES spec only defines a basic structure for communicating "application" data between trading partners. AutoCare.org
provides an XSD schema file for validating the structural correctness of the ACES xml file. The spec does not clearly define
rules about the quality of the data conveyed. For example, a common quality problem is an "overlap".
This is where two different "App" nodes in the xml refer to the same basevehicle with different partnumbers. The real-world
consequence of this is that the end consumer is faced with two different parts for his/her car and no clear understanding 
of which is the right choice. There are many other possible quality problems that could be found in a valid ACES file. It is
generally up to the the data receiver to assess the acceptability of an ACES file and give feedback to the supplier. Many 
data suppliers (part manufacturers) employ sophisticated catalog management systems to house and export ACES files for their
trading partners. These systems generally include quality assurance checks on the outbound data. ACESlint is intended to be
fast and simple "second opinion" of an outbound file, or a quick cleanliness checker for an inbound file before spending time
to import a file of unknown quality. On a typical workstation-grade PC, ACESlint will process about 100,000 applications
per minute, and consume about 400MB per 100,000 applications.

 ACESlint's source is completely contained in a ".c" file. It is intended to be compiled and run on a Linux-ish system. 
It requires libxml2 to be present on the local system. If MySQL support is compiled-in, you can specify a database to validate 
VCdb codes against. Obvioulsy, that database is expected to have a very specific structure (see below). If an ACES xml file is 
linted without referencing a database, only partial validation will be performed (duplicates, overlaps and CNC overlaps). 
If a VCdb database is provided, extended validation is done on the file (basevehicle id validation, attribute code validation 
and attribute combinations validation). Providing a database will also allow the tool to translate VCdb attribute codes and 
basevehicle id's into human-readable values for easier consumption of the results.

If you are interested in using/testing/contributing, feel free to contact Luke Smith lsmith@autopartsource.com. Our industry
can benefit from open-source tools and collaboration. 


#Compilation
##with mysql support
``gcc -o aceslint `xml2-config --cflags` aceslint.c `xml2-config --libs` -L/usr/lib/mysql -lmysqlclient -lz -DWITH_MYSQL``


##without mysql support - VCdb features will be disabled
``gcc -o aceslint `xml2-config --cflags` aceslint.c `xml2-config --libs` -L/usr/lib/mysql -lmysqlclient -lz``


#Running

###command-line switches:
* -d &lt;database name&gt; (example vcdb20161231)
* -h &lt;database host&gt; (optional - "localhost" is assumed)
* -u &lt;database user&gt; (optional - "" is assumed)
* -p &lt;database password&gt; (optional - "" is assumed)
* -v &lt;verbosity level&gt; (optional - 1 is assumed)
* -extractitems (surpress all other output and dump distinct list of part number found in the input file)
* -ignorenaitems (ignore apps with "NA" as the part number)

##example 1 (simple database-less audit)

``aceslint ACES_3_1_AirQualitee_FULL_2017-01-12.xml``

### will produce output like:
``Title:AirQualitee``<br/>
``VcdbVersionDate:2016-12-30``<br/>
``Application count:6512``<br/>
``Duplicate apps:0``<br/>
``Overlaps:211``<br/>
``CNC overlaps:530``<br/>


##example 2 (referencing a specific database for code validation)

aceslint ACES_3_1_AirQualitee_FULL_2017-01-12.xml -d vcdb20171231

### will produce output like:
``Title:AirQualitee``<br/>
``VcdbVersionDate:2016-12-30``<br/>
``Application count:6512``<br/>
``Invalid basevids:0``<br/>
``Invalid vcdb codes:0``<br/>
``Invalid vcdb configurations:0``<br/>
``Duplicate apps:0``<br/>
``Overlaps:211``<br/>
``CNC overlaps:529``<br/>



#Database Creation
The VCdb ("Vehicle Configuration database") is published by AutoCare.org to subscribers on a monthly basis. The database contains 73 tables. Several 
different file formats are provided by AutoCare. We (AutoPartSource) use the "ASCII Text" version. It consists of 73 individual tab-delimited text files
that are named like: "20170127_BaseVehicle.txt". These files are delivered in a zip container. The shell script "rename_VCDB_import_files.sh" will 
rename all the text files to the generic table names that the database inporter script "load_VCDB_tables.sql" expects. VCDB_schema.sql contains the 
table creation SQL statements for making an empty VCdb on a MySQL server. 










