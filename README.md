# PostgreSQL Active Processes

LIST ACTIVE PROCESSES WITH TERMINATE COMMAND

```
SELECT 
   now()-query_start as running_since,
   pid, 
   datname,
   usename, 
   application_name, 
   client_addr, 
   left(query,2000),
   'SELECT pg_terminate_backend(pid) FROM pg_stat_activity WHERE pid = ' || pid || ';'
FROM pg_stat_activity 
WHERE state in ('active','idle in transaction') 
  AND (now() - query_start) > interval '1 seconds';
``` 

LIST ACTIVE PROCESSES
```
SELECT
  -- pg_terminate_backend(pid),
  pid,
  pg_blocking_pids(pid) as blocked_by,
  application_name,
  query_start,
  now() - pg_stat_activity.query_start AS duration,
  usename,
  datname,
  client_addr,
  coalesce(wait_event_type||'/'||wait_event,'') as wait,
  query,
  state,
FROM pg_stat_activity 
WHERE pid <> pg_backend_pid()
  AND state <> 'idle'
  -- AND (now() - pg_stat_activity.query_start) > interval '1 minutes'
ORDER BY query_start desc nulls last
```
