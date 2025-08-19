# High-level view of storing data in a database


{{< figure src="/images/databases/records-pages-files.png" title="Records, bytes, tables, pages and files" >}}

* **Record** - a set of information grouped together, where the different pieces of information can have different types;
* **Byte representation of a record** - how the record is actually stored in disk as bytes. Since records can have fields of different types, not all fields take up the same amount of bytes. Additionally, fields can be of fixed size, such as `char`, or of variable size, such as `varchar`. The specification of the types that compose a record is called a schema;
    * There are many ways a record can be represented in disk. For example, records can be of **fixed** length or of **variable** length and this obviously changes how we store and access them. However, the goals are always the same: records should be compact in memory/disk and there should be fast access to their fields;
* **Table** - a collection of records sharing a schema;
* **Page** - a chunk of sequential bytes that composes a unit of transfer for disk read/write and that can hold multiple records;
    * Much like records, the way page layout is handled is also affected by whether or not the records are of fixed length. Most notably, the records in a page can be **packed** or **unpacked**, where packed refers to not having "empty" bytes between records in a page;
* **File** - a logical collection of pages that compose a table. Note that tables can span multiple files or even multiple machines;
    * Files themselves in a database can be structured in a variety of ways: unordered heap files (records placed arbitrarily across pages), clustered heap files (records and pages are grouped), sorted files (pages and records are in sorter order), index files (B+ trees, linear hashing, etc., may contain records or point to records in other files), etc.

*Note that we've used a slotted page as an example, but that is just one out of many possible ways to manage records in a page.*

