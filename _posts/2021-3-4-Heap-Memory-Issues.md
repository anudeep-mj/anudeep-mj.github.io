**Background and Problem:**

When you are building a service that can accept large files > 500mb, One issue that can have a profound affect on your app performance is JVM heap space memory used.

**Test case:**
Java sdk created which scans documents of upto 4gb for content and virus scanning. It uses Tika for parsing to find macros and other content related sercurity issues.
File being uploaded is a CSV file.

**Problem:**
When a 1.7 GB file was being uploaded, the memory being consumed was around 7 GB! So how do we handle this issue ?

Solution/Methodology:
Of course every issue has its own context. The issue we found was two-fold.

**##First issue:**
When a CSV file was being parsed, we were loading the whole row before iterating on it using: 

         final List<CSVRecord> records = csvParser.getRecords();
  
Lesson learnt was not to load the whole data but use Iterable instead.

         final Iterable<CSVRecord> records = CSVParser.parse(reader, CSVFormat.DEFAULT);

**##Second issue:**
When you are looking for macros in a MSOffice file, you only need its metadata not the whole body too.

Basically we had to modify the recusive parser from

         final RecursiveParserWrapperHandler handler = new RecursiveParserWrapperHandler( 
                      new BasicContentHandlerFactory(BasicContentHandlerFactory.HANDLER_TYPE.XML, -1)); 
            
to

         final RecursiveParserWrapperHandler handler = new RecursiveParserWrapperHandler( 
                      new BasicContentHandlerFactory(BasicContentHandlerFactory.HANDLER_TYPE.IGNORE, -1)); 
                      
Lesson learnt: When we are parsing files, always focus on what you need and do not think getting the whole data will have no affect. This simple change made a difference of 5 GB of memory!


**Tools used:**

Some tools used during the analysing:
jvisualvm
jconsole
ibm heap analyser
eclipse mac
java_opts can be changed as per to get heap dump: https://stackoverflow.com/questions/542979/using-heapdumponoutofmemoryerror-parameter-for-heap-dump-for-jboss
