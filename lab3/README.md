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

Example code with 4 threads and default # of entries per thread (25000)
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
./hash-table-tester -t 4 -s 100000
#Generation: 18,207 usec
#Hash table base: 49,719 usec
#  - 0 missing
#Hash table v1: 130,895 usec
#  - 0 missing
#Hash table v2: 20,517 usec
#  - 0 missing
```
Version 1 is a little slower/faster than the base version. As we have to deal with the overhead of locks both with the locking and unlocking itself as instructions and spin waiting where a thread that is currently in the mutual exclusion section is preempted by a thread that is waiting for the lock which can only spin during its turn to run.

## Second Implementation
In the `hash_table_v2_add_entry` function, I allowed parallel access to different hash table entries by locking the operation at a lower granularity. In particular, I locked the access at the entry level as modifying a linked list under a different hash does not have a race condition with the current hash. There is no way to lock any further as the expensive part of add_entry is the get_list_entry which in the worst case will iterate through the entire linked list. I did precompute the new list entry as from my testing mainly new keys are added so precalculating is a good measure to take under this circumstance.

### Performance
```shell
./hash-table-tester -t 4 -s 200000
#Generation: 150,482 usec
#Hash table base: 8,344,190 usec
#  - 0 missing
#Hash table v1: 10,926,012 usec
#  - 0 missing
#Hash table v2: 3,772,498 usec
#  - 0 missing
```

It performs roughly linearly with respect to threads at low counts as full parallelization can only occur when the threads run on different cores. However, as it increases, it becomes less linear both due to the # of cores avaliable (most computers do not have 4+ cores) and the increased risk of excessive spin waiting. In particular, as the # of threads go up, the likilihood of a thread spin waiting and dropping its priorty on the machine's scheduling system is higher as more threads means more simultaneous processes and more possible locks held at once. Also, as it is hardware dependent, testing on lnxsrv15 is effective for 2-4 cores and on my local vm it is also 2-4 cores as the vm has 4 cores as well. Prior to changing the hardware access of the vm, I could not test my code on my local vm. Also when testing, the number of entries have to be high such that we normalize for variance from scheduling, conflicts and other concurrency related behaviors.

## Cleaning up
```shell
make clean
```