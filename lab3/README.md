# Hash Hash Hash
Simple hash table 

## Building
```shell
make
```

Call make to generate the binary hash-table-tester.

## Running
```shell
TODO how to run and results
```

## First Implementation
In the `hash_table_v1_add_entry` function, I added a lock which locked the entire function such that only one thread could attempt to add an entry at a time. This does prevent race conditions by making the entire function a mutual exclusive at the expense of zero parallelization. There is no worry of deadlocks as there is no circular dependency (once a lock is taken it can always complete).

### Performance
```shell
TODO how to run and results
```
Version 1 is a little slower/faster than the base version. As TODO

## Second Implementation
In the `hash_table_v2_add_entry` function, I TODO

### Performance
```shell
TODO how to run and results
```

TODO more results, speedup measurement, and analysis on v2

## Cleaning up
```shell
TODO how to clean
```