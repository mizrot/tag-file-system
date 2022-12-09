---
Url:"https://dash.harvard.edu/bitstream/handle/1/5136361/Seltzer%20-%20Hierarchical%20File%20Systems%20are%20Dead.pdf"
---
#file-system #project #tag-file-system
## Introduction
Two categories of storing the data: database and filesystem. If one wants to define complex relation and have no direct access to the data stored he will use database, on the contrary if he wants more simple abstraction and have direct control over the data, he will use hierarchical file systems. But over the decades, situation has evolved now instead of average 300 MB storage in 1992 we have 300 GB in 2009, representing a three order of magnitude increase. But files didn't went far from their antecedent's size. In nowadays user may have gigabytes of media on a single pc. So problem of tracking emerges.
We learned tagging from search engines (google searching or MacOS Spotlight). But search engines use databases as foundation, therefore have their weaknesses such as heavy-weight a solution, not idealy optmized for a given task or merely painful to install and manage.
Authors of scientific literature started to arouse interest in the mix of file systems with databases yet in the beginning of 90s (look index 1,2).
> We believe it is time to finally get  
rid of the hierarchical namespace it in favor of a more  
search-friendly storage system. The hierarchical direc-  
tory model is an increasingly irrelevant historical relic,  
and its burial is overdue.
\- Margo Seltzer, Nicholas Murphy

Hierarchical namespace have following problems:
1. **Irrelevance** (file don't correspond to the folder or user have diffuculties to find certain file)
2. **Restrictiveness**(there's no canonical way of catigorization of a piece of data, but hierarchical namespaces are falsely imposing one)
3. **Performance Limiting** (hotspots exsistance and problematic search)

The basic modern filesystem should have:
1. **Backwards compatibility** (in interface or disk layout)
2. **Separate naming from  access** (name shouldn't describe how file stored)
3. **Data agnostic** (every application treat filesystem differently, because filesystem treat data items as opaque sets of bytes)
 4. **Direct access to data** (application needs an item it gets the item)

## hFAD file System
hFad(Hierarchical Filesystems are Dead) - file system based on objects, but unlike traditional objects-based storage devices,  every object in the system is byte-accessible and implementation of direct control presented as object's ID (every object-file has it's own ID).
### API
hFAD API consists of two components: naming interface and access interface. (see picture below)

![[hFAD_diagram.png]]
The naming interfaces map tagged search-terms to objects. After object was located, the access interface can operate.
#### Naming Interfaces
Object's name identifies by one or more tag/value pairs. A tag can tell how to interpret the value as well as in which index object's value is located. Nameing operations can return multiple items. Moreover, no query need uniquely define a data item. Only the identifier for the data in the OSD layer must be unique (object's ID).
| Use          | Tag      | Value             |
| ------------ | -------- | ----------------- |
| POSIX        | POSIX    | pathname          |
| Search       | FULLTEXT | term              |
| Manual       | USER     | logname           |
|              | UDEF     | annotations       |
| Applications | APP      | application name  |
| FastPath     | ID       | object identifier |
|              | USER     | logname           |

#### Access Interfaces 
As our file system implements full control over bytes we need certain way to insert into the middle of objects and [[Truncate]] from anywhere in the file. To support it we according to POSIX add an insert and truncate call.

#### Index Stores
The efficency of this system mostly rely on appropriate way of retrieving the objects IDs. For example, a key/value store suffices for simple attributes but not for full-text, and neither a full-text index nor a key/value store will apply for image indexing. So it's primary for support of arbitrary index types.
#### OSD Layer
This layer represents abstraction for a uniquely identified container of bytes. Each such container(object) has it's own attributes. The OSD is comparable to the ZFS Data Managemnet Unit (DMU), but unlike DMU, OSD does not provide collections of objects(objsets).

#### Implementation
Linux/FUSE as basis. Berkeley DB atop raw device and the storage allocator. We use Berkeley Data Base to store all string indecies and metadata with objects, where key item is a file offsets and whose data items are the disk adresses and lengths corresponding to those files.
## References
```bibtex
@inbook{ The design and implementation of the inversion file system,
title = "In Proceedings of the 1993 USENIX Winter Conference",
author = "Olson",
year = "1993",
pages = "205-218"
index = "1"
}
```

```bibtex
@inbook{Semantic File Systems,
aurthor= "Gifford",
title = "In Proceedings of the 13th ACM Symposium on Operating Systems Principles",
year = "1991",
pages = "16-25"
index = "2"
}
```

```bibtex
@inbook{Stonebraker Commun ACM,
title = "Operating system support for database management",
author= "Stonebraker M",
pages= "412-418"
index="3"
}
```