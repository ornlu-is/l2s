# CS186Berkeley Lecture Notes


A database is a large, organized collection of data. Database Management System (DBMS) is software that stores, manages and facilitates access to data.

## Architecture of a DBMS

{{< figure src="/images/databases/records-pages-files.png" title="Overview of master and worker nodes in Kubernetes" >}}

Client and server, where SQL is typed in some client/application. 

Inside the DBMS there's a number of modules. The first module is the Query Parsis and Optimization module whose purpose is to parse, check, verify the SQL and translate it into an efficient relational query plan.

The Relational Operators are the actual operators used in relational query plan. They are the individual algorithms taht are put together to execute a data flow. That data flow operates on records and files pumping data through the operatores to generate answers to your query.

Then comes Files and Index Management whose purpose is to organize tables and records as groups of pages in a logical file. 

Then comes Buffer Management, providing the illusion that we are operating in RAM instead of disk, maps the disk to RAM.

Then comes Disk Space Management transalates page requests from the buffer manager into physical bytes on one or more devices.

Each layer abstracts the layer below. Some assumptions are made across these layers.

There are two cross-cutting modules that are related to storage and memory management:
* Concurrency control
* Recovery


## Storage Media (Hardware)

### Disks

Most database systems were originally designed for magnetic disks.
* Disk are a mechanical anachronism!
* Instilled design ideas that apply to using solid state disks as well

Major implications!
* No "pointer derefs". Instead, an API:
  * READ: transfer "page" of data from disk to RAM;
  * WRITE: transfer "page" of data from RAM to disk;
* Both API calls are very, very slow!
  * Plan carefully!
* An explicit API can be a good thing
  * Minimizes the kind of pointer errors you see in C

A page is a fixed amount of data, like a 32Kb object. In order to do anything with the data, we need to bring the page to RAM (READ) update it in memory and WRITE it to disk. Note that these are very slow relative to CPU speeds, so we need to be conscious of how we READ/WRITE from disk.

We generally think of storage as being in a hierarchy (increasing order of size but decreasing order of speed):
* Registers
* L1 Cache (0.5ns)
* L2 Cache (7.0ns)
* RAM - for currently used data (100ns)
* SSD - varies by deployment (1,000,000 ns to read 1MB sequentially)
* Disk - database and backup/logs, secondary and tertiary storage (20,000,000ns to read 1MB sequentially)

## Components of a Disk

A magnetic disk drive is a mechanical object made of a bunch of platters and a bunch of heads. The platters sit on a spindle that spins them all together at the same time let's say at about 15000 rpm. And there's this arm assembly that moves in and out to position the reading/writing heads on top of the desired track of the disk. The tracks under heads that are stacked on top of each other compose a cylinder. Only one head reads/writes at any one time. What they write is a multiple of what's called the sector size. It's the part of the track that intersects with one of these geometric sectors. A sector is a fixed size/amount of data and the block/page size that we transfer to and from the disk is a multiple of the sector size.

To access a disk page it takes a certain amount of operations:
* seek time - moving arms to position disk head on track (2-3ms)
* rotational delay - waiting for block to rotate under head (0-4ms)
* transfer time - actually moving data to/from disk surface (0.25ms per 64KB page)

So we want optimize for seek/rotational delays since those are the most expensive operations.

## Flash memory (Solid State Disks)

Current generation is called NAND generation:
* Allows very fine reads 4-8Kb reads, but writes are coarse-grain (1-2Mb writes)
* For a given memory cell, after 2k-3k erasures the cell will usually fail, so we don't want to have hot spots on the disk, so if we have data that is frequently written, we need to move it around on the flash disk, this is called wear leveling
* Write amplification: big units, need to reorg for wear and garbage collection.

Reads are fast and predictable, reading sequential data is about the same speed as random reads. 

Writes on the other hand are not fast. Slower for random than it is for sequential writes (4x difference).

Flash can be 1-10x the bandwidth of ideal HDD, but in practice if there are non-sequential reads, we get 10-100x the bandwidth for random I/Os.

Locality matters for both Flash and Hard Disks. Reading/writing to far away blocks on disk requires slow seek/rotation delay. Writing two far away blocks on SSD can require writing multiple much larger units. Ultimately, disks offer about 10x the capacity per $.

## Disk Space Management

Most DBMS store information on disks and SSDs:
* Disks are a mechanical anachronism (slow)
* SSDs are faster with costly writes and still slow relative to memory

### Block level storage

Disk drives offer and and interface to read and write large chunks of sequential bytes called a block. Blocks being sequential on a disk just means they have some order and that getting the next block is cheaper than getting a far away block.

One of the things we want to do when we get a block of the disk is maximize its usage. If we're gonna pay to do a read or write, we want to amortize that cost, we want to make the most out of that cost because it's going to include the seek delay, the rotational delay and then even with SSDs the write cost is going to be high.

Some of the ways we're going to do this is predicting feature behavior. So we're going to do things like keep blocks in memory if we think they're going to be popular, if we think they are going to be accessed a lot (caching). We might even pre-fetch blocks from the disk because we think they're going to be accessed soon, getting them from the disk into memory before it's even accessed. 

With writes we can buffer them. We hold them in a memory buffer and write several sequential blocks at the same time.

### A note on terminology

* Block - unit of transfer for disk read/write 
* Page - a common synonym for "block"

For the purpose of this document, block and page are synonyms. In some textbooks, page refers strictly to a block that is currently in memory.

### Arranging blocks on disk

When we talk about blocks on disk it's important to have a notion of how close they are to each other, so we'll define the notion of next block on disk.

Sequential blocks on the same track are considered to be very close. You can think of the next block as the one that will rotate under the head. When we are done rotating over all the blocks on the same track then the blocks on the same cylinder would be the next closest (disk arm fixed, different platter). Across cylinders is the next degree of closeness.

We will arrange file pages sequentially by next on disk, which minimizes seek and rotational delay. 

Moreover, for sequential scans, we will pre-fetch several blocks at a time (whole track or even cylinder).

Disk space management module is at the very bottom of the DBMS. Its purpose is three-fold:
* Maps pages to locations on disk
* Loads pages from disk to memory
* Saves pages back to disk and ensures writes

Higher levels of the module hierarchy (namely the buffer management module) call upon this layer to:
* read/write a page
* allocate/de-allocate logical pages form the disk

### How is disk management implemented

Proposal 1 - very low level where the DBMS directly communicates with the storage device, bypassing any feature of the OS. It's fast but what happens when the device changes? So now the software has to be ported to the new device (and you will eventually need a new device because they do not last forever).

Proposal 2 - let the OS worry about the device, using its Filesystem interface:
* Allocate single large contiguous file on a nice empty disk and assume sequential/nearby byte access are fast
* Most FS optimize disk layout for sequential access
* DBMS "file" may span multiple FS files on multiple disks/machines

Summary: Disk Space Management
* Provides API to read and write pages to device
* Page: block level organization of bytes on disk
* Provides "next" locality and abstracts FS/device details

## Overview: Representations

Logically we have a table with rows and columns and we map that onto a file with pages and blocks. The table itself is made out of logical records and the records themselves need to be represented somehow. They are represented in memory as arrays of bytes and then those bytes are packed onto pages in memory.

So the files got pages on disk and each of those pages can be brought onto memory and they contain a bunch of bytes and those bytes represent records in tables.

Tables stored as logical files:
* Consist of pages
  * Pages contain a collection of records

Pages are managed:
  * On disk by the disk space manager: pages read/written to physical disk/files
  * In memory by the buffer manager: higher levels of DBMS only operate in memory

## Files of Pages of Records

* DB file: a collection of pages, each containing a collection of records
* API for higher layers of the DBMS:
  * Insert/delete/modify record
  * Fetch a particular record by record ID
    * Record ID is a pointer encoding pair of (page ID, location on page)
  * Scan all records
    * Possibly with some conditions on the records to be retrieved
* Could span multiple OS files and even machines
  * Or "raw" disk devices

Files themselves in the database can be structure in a variety of ways:
* Unordered heap files - records placed arbitrarily across pages
* Clustered heap files - records and pages are grouped
* Sorted files - pages and records are in sorted order
* Index files - B+ trees, linear hashing, etc. May contain records or point to records in other files

# Unordered heap files

* Collection of records in no particular order - not to be confused with the heap data structure
* As file shrinks/grows, pages are (de)allocated
* To support record level operations, we must:
  * Keep track of the pages in a file
  * Keep track of free space on pages
  * Keep track of the records on a page

## Heap file implemented as a List
* Header page ID and heap file name stored elsewhere
  * Database catalog
* Each page contains 2 "pointers" plus free space and data
* What is wrong with this? How do I find a page with enough space for a 20 byte records

The header page is going to have 2 pointers:
* 1 to a doubly linked list of full pages that have no more room for inserts
* and 1 to a doubly linked list of pages with free space

Slow to find a page that has capacity for our insert.

## Better: usa a page directory

Directory entries include:
* Number of free bytes on the referenced page and a pointer to that page
* Header pages accessed often -> likely in cache
* Finding a page to fit a record required far fewef page loads than linked list because one header page load reveals free space on many pages

Has multiple header pages (which compose the directory).

# Page Layout

## Page basics: the header

We're gonna have a header zone in the page which is gonna have metadata about the page, information about what's contained on the page. May contain:
* Number of records
* Free space
* Maybe a next/last pointer
* Bitmaps, slot table

Things to address in the design of a page:
* Are records all going to be of the same length? Fixed vs variable length
* We need to be able to find records by record ID (page, location in page). How can we make sure these are stable over time
* How do we add and delete records?

## Options for page layouts

Depends on:
* record length (fixed or variable)
* Page packing (packed or unpacked) in terms of its free space

Pages are nothing more than a linear array of bytes on the disk:
* 1 byte per position
* memory addresses are ordered
* Disk addresses are ordered

## Fixed length records, packed layout

* Pack records densely after the header
* record ID
  * page ID, record number in page
  * We know the offset from the start of the page (because of fixed length)
* Easy to add: just append
* Deletion is a bit trickier because we have to repack the page
  * Packed implies we need to re-arrange the page
  * Record IDs that were pointing into this page from elsewhere have to be changed because the record ID numbers changed, this can be expensive

## Fixed length, unpacked layout

* Extra cost is to keep a bitmap in the header that has a bit for each slot on the page
* record ID is the page ID
* The slot number is the page
* Insert just find an empty slot
* Delete just clear bit
* No need to change the outside references

## Variable length records

* How do we know where each record begins?
* What happens when we add and delete records?

We are going to put the metadata in the end of the page (footer) instead of header. Next we are going to fill that footer with a somewhat different kind of metadata. We're gonna have what's called a slot directory.

* Introduce slot directory in footer
  * Pointer to free space, the place where new entries are introduced
  * Length + pointer to beginning of record
    * slots are stored in reverse order
* Record ID = location in slot table (from the right)
* Delete record: set slot directory to null pointer, doesn't affect pointers to other records
* Insert a record
  * Place record in free space on page
  * We look at where the free space pointer points
  * Create a new entry for it in the slot table
  * Update the free space pointer to account for the space we just used

The problem that arises is that we get free space fragmentation, we might want to consolidate it to make room for a big record. In this case we pack the page and change the pointers.

* Reorganize data on page
  * Is this safe?
    * Yes this is safe because record IDs don't change
* When should I reorganize?
  * We could re-organize on delete
  * Or wait until fragmentation blocks record addition and then reorganize

# Record Layout

How to pack fields in a record.

* In a relational each record in the table has some fixed type
* We're going to assume that the system catalog stores the schema
  * no need to store type information with records (records don't need to be self-describing)
  * catalog is just another table
* Goals:
  * Records should be compact in memory & disk format
  * Fast access to fields

Easy case: fixed length fields, interesting case: variable length fields

## Record formats: fixed length

* Field types are the same for all records in a file
  * Type info stored separately in system catalog
* On disk byte representation is the same as in memory (no serialization)
* Finding the i-th field? Done via arithmetic (fast)
* Compact
* If we have nulls we will waste its space and have trouble representing it

## Record formats: variable length

We can add padding to the fields which makes them fixed length, but it's a losing battle because there is always a bigger value.

Another possibility is using delimiters, like commas in CSVs. The problem is that the delimiter might be an actual value in our data. To find a field it requires a scan cost.

The idea is to move all variable length fields to the end of the record and we use a record header to point to the variable length fields.




