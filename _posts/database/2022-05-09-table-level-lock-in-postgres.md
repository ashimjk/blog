---
layout: post
title: Table-Level Lock in PostgreSQL
date: 2022-05-10 12:34 +0545
categories: database
tags: postgres
---

## Tables that provide Stats

- `pg_stat_activity` : shows information related to the current activity of that process.
- `pg_class` : catalogs tables that shows information about table, view, index, etc.
- `pg_locks` : provides information about the locks held by active processes.
- `pg_stat_all_tables` : shows statistics about accesses of the table.
- `pg_stat_all_indexes` : shows statistics about accesses of the index.

## Table-Level Lock Modes

There are many table-level lock modes but these are the important ones for data query and update.

- `ACCESS SHARE` : The`SELECT`command acquires a lock of this mode on referenced tables.
- `ROW SHARE` : The`SELECT FOR UPDATE`and`SELECT FOR SHARE`commands acquire a lock of this mode on the target table(s).
- `ROW EXCLUSIVE` : The commands`UPDATE`,`DELETE`, and`INSERT`acquire this lock mode on the target table.

## Identify Table-Level Lock

```sql
select relname as relation_name, mode, query, pg_locks.*
from pg_locks
         join pg_class on pg_locks.relation = pg_class.oid
         join pg_stat_activity psa on pg_locks.pid = psa.pid
where relname not like 'pg_%';
```

## Identify Blocking Lock

```sql
select activity.pid, activity.usename, activity.query, blocking.pid as blocking_id, blocking.query as blocking_query
from pg_stat_activity as activity
         join pg_stat_activity as blocking on blocking.pid = any (pg_blocking_pids(activity.pid));
```

## Queries to identify Sequence and Index Scan

```sql
select relname, seq_scan, idx_scan, autovacuum_count, last_autovacuum, autoanalyze_count, last_autoanalyze
from pg_stat_all_tables
where relname not like 'pg_%';

select relname, indexrelname, idx_scan
from pg_stat_all_indexes
where relname not like 'pg_%';
```

## Reference

- [Lock Mode Types](https://www.postgresql.org/docs/current/explicit-locking.html#LOCKING-TABLES)
