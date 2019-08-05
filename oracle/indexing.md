# Indexing Essentials

An index is an optional structure, associated with a table or table cluster, that can sometimes speed data access. By creating an index on one or more columns of a table, you gain the ability in some cases to retrieve a small set of randomly distributed rows from the table. Indexes are one of many means of reducing disk I/O.

By default, Oracle creates B-tree indexes.

## Keys and Columns

A key is a set of columns or expressions on which you can build an index. Although the terms are often used interchangeably, indexes and keys are different. Indexes are structures stored in the database that users manage using SQL statements. Keys are strictly a logical concept.

The following statement creates an index on the customer_id column of the sample table orders:

```sql
CREATE INDEX ord_customer_ix ON orders (customer_id);
```

## Composite Indexes

A composite index, also called a concatenated index, is an index on multiple columns in a table. Columns in a composite index should appear in the order that makes the most sense for the queries that will retrieve data and need not be adjacent in the table.

For example, suppose an application frequently queries the last_name, job_id, and salary columns in the employees table. Also assume that last_name has high cardinality, which means that the number of distinct values is large compared to the number of table rows. You create an index with the following column order:

```sql
CREATE INDEX employees_ix
   ON employees (last_name, job_id, salary);
```

Queries that access all three columns, only the last_name column, or only the last_name and job_id columns use this index. In this example, queries that do not access the last_name column do not use the index.

## Unique and Nonunique Indexes

Indexes can be unique or nonunique. Unique indexes guarantee that no two rows of a table have duplicate values in the key column or columns. For example, no two employees can have the same employee ID. Thus, in a unique index, one rowid exists for each data value. The data in the leaf blocks is sorted only by key.

Nonunique indexes permit duplicates values in the indexed column or columns. For example, the first_name column of the employees table may contain multiple Mike values. For a nonunique index, the rowid is included in the key in sorted order, so nonunique indexes are sorted by the index key and rowid (ascending).

## Types of Indexes

### B-tree indexes

These indexes are the standard index type. They are excellent for primary key and highly-selective indexes. Used as concatenated indexes, B-tree indexes can retrieve data sorted by the indexed columns. B-tree indexes have the following subtypes:

![btree-index.png](images/btree-index.png)

**Index-organized tables**

An index-organized table differs from a heap-organized because the data is itself the index. See "Overview of Index-Organized Tables".

**Reverse key indexes**

In this type of index, the bytes of the index key are reversed, for example, 103 is stored as 301. The reversal of bytes spreads out inserts into the index over many blocks. See "Reverse Key Indexes".

**Descending indexes**

This type of index stores data on a particular column or columns in descending order. See "Ascending and Descending Indexes".

**B-tree cluster indexes**

This type of index is used to index a table cluster key. Instead of pointing to a row, the key points to the block that contains rows related to the cluster key. See "Overview of Indexed Clusters".

### Bitmap and bitmap join indexes

Bitmap index are designed to index the columns which have low number of distinct values. These can be:

![bitmap-index-col.png](images/bitmap-index-col.png)

Oracle bitmap indexes are very different from standard b-tree indexes. In bitmap structures, a two-dimensional array is created with one column for every row in the table being indexed. Each column represents a distinct value within the bitmapped index. This two-dimensional array represents each value within the index multiplied by the number of rows in the table.

![bitmap-simple.png](images/bitmap-simple.png)

The real benefit of bitmapped indexing occurs when one table includes multiple bitmapped indexes. Each individual column may have low cardinality. The creation of multiple bitmapped indexes provides a very powerful method for rapidly answering difficult SQL queries.

![bitmap-index-merge.jpg](images/bitmap-index-merge.jpg)

Also, remember that bitmap indexes are only suitable for static tables and materialized views which are updated at nigh and rebuilt after batch row loading.  If your tables experience multiple DML's per second, BE CAREFUL when implementing bitmap indexes!

**1 - 7 distinct key values** - Queries against bitmap indexes with a low cardinality are very fast.

**8-100 distinct key values** - As the number if distinct values increases, performance decreases proportionally.

**100 - 10,000 distinct key values** - Over 100 distinct values, the bitmap indexes become huge and SQL performance drops off rapidly.

**Over 10,000 distinct key values** - At this point, performance is ten times slower than an index with only 100 distinct values.

## What is the difference between a btree and a bitmap index

Internally, a bitmap and a btree indexes are very different, but functionally they are identical in that they serve to assist Oracle in retrieving rows faster than a full-table scan.  The basic differences between b-tree and bitmap indexes include:

1:  Syntax differences:  The bitmap index includes the "bitmap" keyword.  The btree index does not say "bitmap".

2: Cardinality differences:  The bitmap index is generally for columns with lots of duplicate values (low cardinality), while b-tree indexes are best for high cardinality columns.

3: Internal structure differences:  The internal structures are quite different.  A b-tree index has index nodes (based on data block size), it a tree form:

![b-tree.gif](images/b-tree.gif)

A bitmap index looks like this, a two-dimensional array with zero and one (bit) values:

![bitmap.jpg](images/bitmap.jpg)

Ref:- http://www.dba-oracle.com/t_difference_between_btree_and_bitmap_index.htm

## Selecting an Index Strategy

Use the following guidelines for determining when to create an index:

* Create an index if you frequently want to retrieve less than 15% of the rows in a large table. The percentage varies greatly according to the relative speed of a table scan and how clustered the row data is about the index key. The faster the table scan, the lower the percentage; the more clustered the row data, the higher the percentage.
* Index columns used for joins to improve performance on joins of multiple tables.
* Primary and unique keys automatically have indexes, but you might want to create an index on a foreign key;
* Small tables do not require indexes; if a query is taking too long, then the table might have grown from small to large.

Some columns are strong candidates for indexing. Columns with one or more of the following characteristics are candidates for indexing:

* Values are relatively unique in the column.
* There is a wide range of values (good for regular indexes).
* There is a small range of values (good for bitmap indexes).
* The column contains many nulls, but queries often select all rows having a value. In this case, a comparison that matches all the non-null values, such as:
  WHERE COL_X > -9.99 *power(10,125)

  is preferable to

  WHERE COL_X IS NOT NULL

  This is because the first uses an index on COL_X (assuming that COL_X is a numeric column).

Columns with the following characteristics are less suitable for indexing:

* There are many nulls in the column and you do not search on the non-null values.

## Limit the Number of Indexes for EachTable

The more indexes, the more overhead is incurred as the table is altered. When rows are inserted or deleted, all indexes on the table must be updated. When a column is updated, all indexes on the column must be updated.

You must weigh the performance benefit of indexes for queries against the performance overhead of updates. For example, if a table is primarily read-only, you might use more indexes; but, if a table is heavily updated, you might use fewer indexes.

## Choose the Order of Columns in Composite Indexes

Although you can specify columns in any order in the CREATE INDEX command, the order of columns in the CREATE INDEX statement can affect query performance. In general, you should put the column expected to be used most often first in the index. You can create a composite index (using several columns), and the same index can be used for queries that reference all of these columns, or just some of them.


![index-seq.gif](images/index-seq.gif)

Assume that there are five vendors, and each vendor has about 1000 parts.

Suppose that the VENDOR_PARTS table is commonly queried by SQL statements such as the following:

SELECT * FROM vendor_parts
    WHERE part_no = 457 AND vendor_id = 1012;

To increase the performance of such queries, you might create a composite index putting the most selective column first; that is, the column with the most values:

CREATE INDEX ind_vendor_id
    ON vendor_parts (part_no, vendor_id);

Composite indexes speed up queries that use the leading portion of the index. So in the above example, queries with WHERE clauses using only the PART_NO column also note a performance gain. Because there are only five distinct values, placing a separate index on VENDOR_ID would serve no purpose.

## Function-Based Indexes

A function-based index is an index built on an expression. It extends your indexing capabilities beyond indexing on a column. A function-based index increases the variety of ways in which you can access data.

### Advantages of Function-Based Indexes

```sql
CREATE INDEX Idx ON Example_tab(Column_a + Column_b);
SELECT * FROM Example_tab WHERE Column_a + Column_b < 10;
```

The optimizer can use a range scan for this query because the index is built on (column_a + column_b). Range scans typically produce fast response times if the predicate selects less than 15% of the rows of a large table. The optimizer can estimate how many rows are selected by expressions more accurately if the expressions are materialized in a function-based index. (Expressions of function-based indexes are represented as virtual columns and ANALYZE can build histograms on such columns.)

