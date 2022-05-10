---
layout: post
title: Using Index in PostgreSQL and Oracle
date: 2022-05-09 15:26 +0545
categories: database
tags: postgres oracle
---

Using an index is one of the common ways to optimize performance. And there are things, need to be considered while creating it. Like,
- what happens when multiple fields need to be used for indexing?
- what will be the order for those fields?
- do multiple indexes going to overlap each other?
- what is the limit for creating an index for any table?

These are the few concerns, that will guide you in creating a better index. But in this article, we will only touch the surface level and know how to start using it.

So, let's create an index and see, how to verify its usage.

## Run PostgreSQL and Oracle
```docker
version: '3'
services:
  postgres:
    container_name: postgres
    image: postgres
    ports:
      - "5432:5432"
    environment:
      - POSTGRES_DB=dummy
      - POSTGRES_PASSWORD=postgres
    restart: always

  oracle:
    container_name: oracle
    image: ashimjk/oracle-ee-12c
    ports:
      - "1521:1521"
```

PostgreSQL
```config
url = jdbc:postgresql://localhost:5432/dummy
username = postgres
password = postgres
```

Oracle
```config
url = jdbc:oracle:thin:@localhost:1521/ee.oracle.docker
username = system
password = oracle
```

## Create Table
```sql
create table dummy
(
    id        int not null primary key,
    reference varchar(255)
);
```

## Create Index
```sql
create index dummy_reference_idx ON dummy (reference);
```

## Dummy Data
```sql
insert into dummy values (1, 'ref1');
insert into dummy values (2, 'ref2');
insert into dummy values (3, 'ref3');
insert into dummy values (4, 'ref4');
insert into dummy values (5, 'ref5');
insert into dummy values (6, 'ref6');
insert into dummy values (7, 'ref7');
insert into dummy values (8, 'ref8');
insert into dummy values (9, 'ref9');
insert into dummy values (10, 'ref10');
```

### Check index usage PostgreSQL

- Use `explain` to verify the usage of the index

```sql
explain select * from dummy where id = 1;

-- OUTPUT
Index Scan using dummy_pkey on dummy  (cost=0.14..8.16 rows=1 width=520)
  Index Cond: (id = 1)
```

```sql
explain select * from dummy where reference = 'ref1';

-- OUTPUT
Index Scan using dummy_reference_idx on dummy  (cost=0.14..8.16 rows=1 width=520)
  Index Cond: ((reference)::text = 'ref1'::text)
```

- Use `explain analyze` for more detail.

```sql
explain analyse select * from dummy where id = 1;

-- OUTPUT
Index Scan using dummy_pkey on dummy  (cost=0.14..8.16 rows=1 width=520) (actual time=0.034..0.036 rows=1 loops=1)
  Index Cond: (id = 1)
Planning Time: 0.087 ms
Execution Time: 0.058 ms
```

```sql
explain analyse select * from dummy where reference = 'ref1';

-- OUTPUT
Index Scan using dummy_reference_idx on dummy  (cost=0.14..8.16 rows=1 width=520) (actual time=0.018..0.020 rows=1 loops=1)
  Index Cond: ((reference)::text = 'ref1'::text)
Planning Time: 0.077 ms
Execution Time: 0.039 ms

```

### Check index usage Oracle
After executing the query, `dbms_xplan.display` table provides a summary of how the query was executed and which index was used.

```sql
explain plan for select * from dummy where id = 1;
select * from table(dbms_xplan.display);

-- OUTPUT
| Id  | Operation                   | Name         |
----------------------------------------------------
|   0 | SELECT STATEMENT            |              |
|   1 |  TABLE ACCESS BY INDEX ROWID| DUMMY        |
|*  2 |   INDEX UNIQUE SCAN         | SYS_C0018370 |
```

```sql
explain plan for select * from dummy where reference = 'ref1';
select * from table(dbms_xplan.display);

-- OUTPUT
| Id  | Operation                           | Name                |
-------------------------------------------------------------------
|   0 | SELECT STATEMENT                    |                     |
|   1 |  TABLE ACCESS BY INDEX ROWID BATCHED| DUMMY               |
|*  2 |   INDEX RANGE SCAN                  | DUMMY_REFERENCE_IDX |
```

An alternative way to extract the same summary can be configured in various ways. But you need to use sqlplus or any other similar tools:
```sql
set autotrace on explain
select * from dummy where reference = 'ref1';
set autotrace off
```

## Reference
- [Set autotrace in sqlplus](https://dbaora.com/set-autotrace-in-sqlplus/)
- [Index Usage and Index Performance Test in Oracle Database](https://ittutorial.org/index-usage-oracle-index-performance-test-in-oracle-database-real-life/)
