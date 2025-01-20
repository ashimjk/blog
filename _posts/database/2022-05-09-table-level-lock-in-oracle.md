---
layout: post
title: Table-Level Lock in Oracle
date: 2022-05-10 13:06 +0545
categories: database
tags: oracle
---

## Lock mode

The numeric values for this column map to these text values for the lock modes for table locks:

- `0`- NONE: lock requested but not yet obtained
- `1`- NULL
- `2`- ROWS_S (RS): Row Share Lock
    - indicates that the transaction holding the lock on the table has locked rows in the table and intends to update
      them.
- `3`- ROW_X (RX): Row Exclusive Table Lock
    - indicates that the transaction holding the lock has updated table rows or issued`SELECT ... FOR UPDATE`. An RX
      lock allows other transactions to query, insert, update, delete, or lock rows concurrently in the same table.
- `4`- SHARE (S): Share Table Lock
    - A share table lock held by a transaction allows other transactions to query the table (without using
      `SELECT ... FOR UPDATE`), but updates are allowed only if a single transaction holds the share table lock.
- `5`- S/ROW-X (SRX): Share Row Exclusive Table Lock
    - Only one transaction at a time can acquire an `SRX` lock on a given table. An `SRX` lock held by a transaction
      allows other transactions to query the table (except for`SELECT ...``FOR UPDATE`) but not to update the table.
- `6`- Exclusive (X): Exclusive Table Lock
    - This lock is the most restrictive, prohibiting other transactions from performing any type of `DML` statement or
      placing any type of lock on the table.

## Last Executed Query

```sql
select *
from v$sql
where service = 'ee.oracle.docker'
  and module = 'JDBC Thin Client'
order by LAST_ACTIVE_TIME desc;
```

## Lock acquired by Table

```sql
select a.locked_mode,
       c.owner,
       c.object_name,
       c.object_type,
       b.sid,
       b.serial#,
       b.status,
       b.osuser,
       b.machine
from v$locked_object a,
     v$session b,
     dba_objects c
where b.sid = a.session_id
  and a.object_id = c.object_id;
```

## Active Session and Process

```sql
select s.sid,
       s.serial#,
       spid os_pid,
       s.event,
       s.username,
       s.program,
       s.sql_id,
       s.logon_time
from v$session s,
     v$process p
where s.paddr = p.addr
  and s.username = 'SYSTEM'
order by logon_time;
```

## Check Lock Holder/Waiter

```sql
SELECT DECODE(request, 0, 'Holder: ', 'Waiter: ') || sid "Session ID", id1, id2, lmode, request, type
FROM V$LOCK
WHERE (id1, id2, type) IN (SELECT id1, id2, type FROM V$LOCK WHERE request > 0)
ORDER BY id1, request;
```

## Blocking Objects and Sessions

```sql
SELECT B.HOLDING_SESSION || ':' || S1.SERIAL# HOLDING_SID_SRN#,
       S1.USERNAME                            HOLDER,
       W.LOCK_TYPE,
       W.MODE_HELD,
       S1.MACHINE,
       S1.MODULE,
       W.WAITING_SESSION || ':' || S2.SERIAL# WAITING_SID_SRN#,
       S2.USERNAME                            WAITER,
       L.OBJECT_ID,
       O.OBJECT_NAME
FROM DBA_BLOCKERS B,
     DBA_WAITERS W,
     V$LOCKED_OBJECT L,
     DBA_OBJECTS O,
     v$session S1,
     v$session S2
WHERE B.HOLDING_SESSION = W.HOLDING_SESSION
  AND B.HOLDING_SESSION = L.SESSION_ID
  AND B.HOLDING_SESSION = S1.SID
  AND W.WAITING_SESSION = S2.SID
  AND L.OBJECT_ID = O.OBJECT_ID;
```

## Check Session Blocking Session

```sql
select s1.inst_id || ':' || s1.sid || ':' || s1.serial# BLOCKING_INST_SESS_SER#,
       ' IS BLOCKING ' ACTION,
       s2.inst_id || ':' || s2.sid || ':' || s2.serial# BLOCKING_INST_SESS_SER#,
       round(s1.last_call_et / 60)                      FOR_MINUTES
from gv$lock l1, gv$lock l2, gv$session s1, gv$session s2
where l1.block
    > 0
  and l2.request
    > 0
  and l1.id1 = l2.id1
  and l1.id2 = l2.id2
  and l1.sid = s1.sid
  and l1.inst_id = s1.inst_id
  and l2.sid = s2.sid
  and l2.inst_id = s2.inst_id
order by l1.inst_id;
```

## Total Blocking Session Time

```sql
select nvl(max(round(s1.last_call_et / 60)), 0) FOR_MINUTES
from gv$lock l1,
     gv$lock l2,
     gv$session s1,
     gv$session s2
where l1.block > 0
  and l2.request > 0
  and l1.id1 = l2.id1
  and l1.id2 = l2.id2
  and l1.sid = s1.sid
  and l1.inst_id = s1.inst_id
  and l2.sid = s2.sid
  and l2.inst_id = s2.inst_id
order by l1.inst_id;
```

## References

- [# V$LOCKED_OBJECT](https://docs.oracle.com/database/121/REFRN/GUID-3F9F26AA-197F-4D36-939E-FAF1EFD8C0DD.htm#REFRN30125)
- [Data Concurrency and Consistency](https://docs.oracle.com/database/121/CNCPT/consist.htm#CNCPT020)
