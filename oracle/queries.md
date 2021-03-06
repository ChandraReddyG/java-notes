# Oracle Queries

## Displaying an execution plan

```sql
SELECT plan_table_output 
    FROM
    table(dbms_xplan.display_cursor(‘<<sql id>>',
        null,'typical'));
```

## Who is logged-in

There are often times, when it is useful to see what sessions are logged into Oracle and you can do that by querying the v dollar session view. 

```sql
SELECT 
    username, osuser, program, module, machine, terminal, process, to_char(logon_time, 'YYY-MM-DD HH24:MI:SS') as logon_time, status, CASE status
        WHEN 'ACTIVE' THEN NULL
        ELSE last_call_et
        END as idle_time
FROM v$session
WHERE type = 'USER';
```

## Where are my logins coming from

```sql
SELECT 
    username, osuser, program, module, machine, terminal, process, count(1) as login_count
FROM v$session
WHERE type = 'USER'    
GROUP BY username, osuser, program, module, machine, terminal, process;
```

## Here is the query I use to get information about the most intensive statements in the databases I work with.

```sql
 SELECT * FROM  
 (  
   SELECT sql_id, sql_text, executions,   
     elapsed_time, cpu_time, buffer_gets, disk_reads,  
     elapsed_time / executions AS avg_elapsed_time,  
     cpu_time / executions AS avg_cpu_time,  
     buffer_gets / executions as avg_buffer_gets,  
     disk_reads / executions as avg_disk_reads  
   FROM v$sqlstats  
   WHERE executions > 0  
   ORDER BY elapsed_time / executions DESC  
 )  
 WHERE rownum <= 25;  
```

## What SQL Statements are Currently Running in my Oracle Database

```sql
 SELECT  
     s.sid, s.username, s.osuser,   
     s.machine, s.process, s.program, s.module,   
     q.sql_text, q.optimizer_cost,   
     s.blocking_session, bs.username as blocking_user,   
     bs.machine as blocking_machine, bs.module as blocking_module,  
     bq.sql_text AS blocking_sql, s.event AS wait_event,  
     q.sql_fulltext  
   FROM v$session s  
   INNER JOIN v$sql q  
     ON s.sql_id = q.sql_id  
   LEFT OUTER JOIN v$session bs -- blocking sessions  
     ON s.blocking_session = bs.sid  
   LEFT OUTER JOIN v$sql bq -- blocking queries  
     ON bs.sql_id = bq.sql_id  
   WHERE s.type = 'USER';  
```

## Finding Unused Indexes in Oracle

The following query uses the V$sql_plan table to find all the usages of an index, and then joins that information to the all_indexes view.  So we can see what indexes are getting used, but what statements and what indexes aren't getting used with this query.

```sql
 WITH index_usage AS
(
    SELECT pl.sql_id, pl.object_owner, pl.object_name, pl.operation,
            pl.options, count(1) as use_count
        FROM v$sql_plan pl
        WHERE pl.operation = 'INDEX'
        GROUP BY pl.sql_id, pl.object_owner, pl.object_name, pl.operation, pl.options
)
SELECT
        ix.table_owner, ix.table_name, ix.index_name, iu.operation,
        iu.options, ss.sql_text, ss.executions
    FROM all_indexes ix
    LEFT OUTER JOIN index_usage iu
             ON ix.owner = iu.object_owner
             AND ix.index_name = iu.object_name  
    LEFT OUTER JOIN v$sqlstats ss
        ON iu.sql_id = ss.sql_id
    WHERE ix.owner = '<<owner name>>'
    ORDER BY ix.table_name, ix.index_name;
```

## What sessions are consuming most resources

```sql
SELECT
    s.sid, s.username, s.osuser,
    to_char(sm.begin_time, 'HH24:MI:ss') as interval_start,
    to_char(sm.end_time, 'HH24:MI:ss') as interval_end,
    s.machine, s.process, s.program, s.module,
    sm.cpu, sm.pga_memory, sm.logical_reads, sm.physical_reads,
    sm.hard_parses, sm.soft_parses, s.logon_time
FROM v$session s inner join v$sessmetric sm
    on sm.session_id = s.sid
    where s.type = 'USER'    
    order by sm.cpu DESC;
```



## Current Login Sessions

```sql
SELECT 
        username, osuser, program, module,
        machine, terminal, process, 
        to_char(logon_time, 'YYYY-MM-DD HH24:MI:SS') 
            AS logon_time,
        status,
        CASE status
            WHEN 'ACTIVE' THEN NULL
            ELSE last_call_et
        END as idle_time
    FROM v$session
    WHERE type = 'USER';
```

## Where are my logins coming from

```sql
SELECT 
        username, osuser, program, module,
        machine, process, 
        count(1) as login_count
    FROM v$session
    WHERE type = 'USER'
    GROUP BY 
        username, osuser, program, module,
        machine, process;
```        
	
## Currently executing SQL

```sql
SELECT
        s.sid, s.username, s.osuser, 
        s.machine, s.process, s.program, s.module, 
        q.sql_text, q.optimizer_cost, 
        s.blocking_session, bs.username as blocking_user, 
        bs.machine as blocking_machine, bs.module as blocking_module,
        bq.sql_text AS blocking_sql, s.event AS wait_event,
        q.sql_fulltext
    FROM v$session s
    INNER JOIN v$sql q
        ON s.sql_id = q.sql_id
    LEFT OUTER JOIN v$session bs  -- blocking sessions
        ON s.blocking_session = bs.sid
    LEFT OUTER JOIN v$sql bq  -- blocking queries
        ON bs.sql_id = bq.sql_id
    WHERE s.type = 'USER';
```
	
## Session Resource Consumption

```sql
SELECT 
        s.sid, s.username, s.osuser, 
        to_char(sm.begin_time, 'HH24:MI:ss') AS interval_start,
        to_char(sm.end_time, 'HH24:MI:ss') AS interval_end,
        s.machine, s.process, s.program, s.module, 
        sm.cpu, sm.pga_memory, sm.logical_reads, sm.physical_reads, 
        sm.hard_parses, sm.soft_parses,
        s.logon_time        
    FROM v$session s
    INNER JOIN v$sessmetric sm
        ON sm.session_id = s.sid
    WHERE s.type = 'USER'
    ORDER BY sm.cpu DESC;
```

## Statements consuming the most resources

```sql
SELECT * FROM
(
    SELECT sql_id, sql_text, executions, 
        elapsed_time, cpu_time, buffer_gets, disk_reads,
        elapsed_time / executions AS avg_elapsed_time,
        cpu_time / executions AS avg_cpu_time,
        buffer_gets / executions as avg_buffer_gets,
        disk_reads / executions as avg_disk_reads
    FROM v$sqlstats
    WHERE executions > 0
    ORDER BY elapsed_time / executions DESC
)
WHERE rownum <= 25;
```

## Statements performing full table scans

```sql
SELECT 
        pl.object_owner, pl.object_name, 
        pl.sql_id, q.sql_text, q.module, 
        pl.operation, pl.options, pl.cost, pl.cpu_cost, pl.io_cost, 
        q.executions          
    FROM v$sql_plan pl
    INNER JOIN v$sql q
        ON pl.sql_id = q.sql_id   
    WHERE 
        (pl.operation = 'TABLE ACCESS' AND pl.options = 'FULL')
        OR (pl.operation = 'INDEX' AND pl.options = 'FAST FULL SCAN')
        OR (pl.operation = 'INDEX' AND pl.options = 'FULL SCAN (MIN/MAX)')
        OR (pl.operation = 'INDEX' AND pl.options = 'FULL SCAN')
    ORDER BY pl.object_owner, pl.object_name;
```
## Displaying an execution plan

```sql
SELECT plan_table_output 
    FROM
    table(dbms_xplan.display_cursor(ë<<sql id>>',
        null,'typical'));
```

## Index Usage

```sql
WITH index_usage AS
(
    SELECT pl.sql_id, pl.object_owner, pl.object_name, pl.operation, 
            pl.options, count(1) as use_count
        FROM v$sql_plan pl
        WHERE pl.operation = 'INDEX'
        GROUP BY pl.sql_id, pl.object_owner, pl.object_name, pl.operation, 
            pl.options
)
SELECT
        ix.table_owner, ix.table_name, ix.index_name, iu.operation,
        iu.options, ss.sql_text, ss.executions
    FROM all_indexes ix
    LEFT OUTER JOIN index_usage iu
             ON ix.owner = iu.object_owner
             AND ix.index_name = iu.object_name   
    LEFT OUTER JOIN v$sqlstats ss
        ON iu.sql_id = ss.sql_id
    WHERE ix.owner = '<<owner name>>'
    ORDER BY ix.table_name, ix.index_name;
```
## Hard Parsing Statistics

```sql
SELECT sn.statistic#, sn.name, s.value
    FROM v$sysstat s
    INNER JOIN v$statname sn
        ON s.statistic# = sn.statistic#
    WHERE sn.name in (
        'parse time cpu', 
        'parse count (hard)')
```

## Queries Usin Literal Values

```sql
WITH statements AS
(
    SELECT force_matching_signature,
        count(1) OVER (PARTITION BY force_matching_signature) AS statement_count,
        row_number() OVER (PARTITION BY force_matching_signature 
            ORDER BY last_load_time DESC) AS row_index,
        parsing_schema_name,
        sql_text
    FROM v$sql
    WHERE force_matching_signature > 0
        AND force_matching_signature <> exact_matching_signature
)
SELECT 
        force_matching_signature, parsing_schema_name, 
        sql_text, statement_count
    FROM statements
    WHERE row_index = 1
        AND statement_count >= 5;
```

## Table Information

```sql
SELECT 
        t.owner, t.table_name, t.num_rows, t.avg_row_len,
        t.blocks AS blocks_below_hwm, t.empty_blocks, 
        s.blocks AS segment_blocks, 
        s.bytes / 1048576 AS size_in_mb, 
        to_char(t.last_analyzed, 'YYYY-MM-DD HH24:MI') 
            AS last_analyzed
    FROM all_tables t
    INNER JOIN dba_segments s
        ON t.owner = s.OWNER AND t.table_name = s.segment_name
    WHERE t.owner = ë<<schema owner>>';
```

## Column Information

```sql
SELECT column_name, avg_col_len, 
        num_distinct, num_nulls, 
    FROM all_tab_columns 
    WHERE table_name = '<<table name>>'
    ORDER BY column_id DESC
```

## Index Information

```sql
SELECT ix.owner, ix.index_name, ix.table_name, 
        ix.distinct_keys, ix.leaf_blocks, 
        s.blocks AS segment_blocks, 
        s.bytes / 1048576 AS size_in_mb, 
        to_char(ix.last_analyzed, 'YYYY-MM-DD HH24:MI') 
            AS last_analyzed
    FROM all_indexes ix
    INNER JOIN dba_segments s
        ON ix.owner = s.OWNER 
            AND ix.index_name = s.segment_name
    ORDER BY ix.table_name, ix.index_name
```

## To Generate execution Plan
Alter Session Set statistics_level=ALL -- gather plan statistics -- Other values TYPICAL (Default), BASIC
select * from table(dbms_xplan.display_cursor(NULL,NULL,'ALLSTATS LAST')) -- Run some query and run this query to get execution plan

select * from table(dbms_xplan.display_cursor(NULL,NULL,'ALLSTATS LAST +ADAPTIVE')) -- from 12 adaptive plans has been added to oracle

Plan Hash value - identifier for plan execution identifier (If there are different execution plans for same sql this value will be different but sqlId will be same)


## Performance Views

select * from v$database; - Active session all information related to database
select * from v$version;
select * from v$instance; -- Instance and database is different

select * from gv$database; - Global sessions
select * from gv$version;
select * from gv$instance; -- Instance and database is different


## ASH (Active Session History) & AWR (Automatic Work Repository)


## Session Monitor 

select * from gv$session -- information about current session. What is going at the moment.

select * from gv$active_session_history; -- Only active session

## Find the session history if it is present in AWR
select * from gv$dba_hist_Active_sess_history;

## IDENITIFY ROW LOC

 select
   c.owner,
   c.object_name,
   c.object_type,
   b.sid,
   b.serial#,
   b.status,
   b.osuser,
   b.machine
from
   gv$locked_object a ,
   gv$session b,
   dba_objects c
where
   b.sid = a.session_id
and
   a.object_id = c.object_id;
   
   ## Active transactions
   select t.inst_id 
       ,s.sid
      ,s.serial#
      ,s.username
      ,s.machine
      ,s.status
      ,s.lockwait
      ,t.used_ublk
      ,t.used_urec
      ,t.start_time
from gv$transaction t
inner join gv$session s on t.addr = s.taddr;
