# Hash Hash Hash

Ryan Chaiyakul, 606028764

Simple concurrency safe hash table to understand locking paradigms.

## Building
```shell
make
```

Call make to generate the binary hash-table-tester.

## Running
```shell
./hash-table-tester -t [threads] -s [entries per thread]
```

Example code with 4 threads at default # of entries per thread (25000)
```shell
./hash-table-tester -t 4
#Generation: 18,207 usec
#Hash table base: 49,719 usec
#  - 0 missing
#Hash table v1: 130,895 usec
#  - 0 missing
#Hash table v2: 20,517 usec
#  - 0 missing
```

## First Implementation
In the `hash_table_v1_add_entry` function, I added a lock which locked the entire function such that only one thread could attempt to add an entry at a time. This prevents race conditions by making the entire function a mutual exclusive at the expense of zero parallelization. There is no worry of deadlocks as there is no circular dependency (once a lock is taken it can always complete).

### Performance
```shell
TODO how to run and results
```
Version 1 is a little slower/faster than the base version. As we have to deal with the overhead of locks both with the locking and unlocking itself as instructions and spin waiting where a thread that is currently in the mutual exclusion section is preempted by a thread that is waiting for the lock which can only spin during its turn to run.

## Second Implementation
In the `hash_table_v2_add_entry` function, I allowed parallel access to different hash table entries by locking the operation at a lower granularity. In particular, I locked the access at the entry level as modifying a linked list under a different hash does not have a race condition with the current hash. In terms of two threads trying to add an entry to the same linked list, the data race occurs when a thread 

### Performance
```shell
TODO how to run and results
```

TODO more results, speedup measurement, and analysis on v2

## Cleaning up
```shell
make clean
```