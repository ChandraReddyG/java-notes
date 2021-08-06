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
GROUP BY username, osuser, program, module, machine, process;
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
FROM v#session s inner join v@sessmetric sm
    on sm.session_id = s.sid
    where s.type = 'USER'    
    order by sm.cpu DESC;
```
