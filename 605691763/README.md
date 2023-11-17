# Hash Hash Hash
TODO introduction
Abhinav Amanaganti 
UID: 605-691-763
## Building
```shell
TODO
```
run 'make clean' if executable already exists
run 'make' to create executable
## Running
```shell
TODO how to run and results
```
run './hash-table-tester' use -t flag for modifying # of threads and -s flag for modifying # of entries
by default, 4 threads are used and 25,000 entries

results of './hash-table-tester -t 4 -s 50000'

[classvti@lnxsrv13 ~/Desktop/lab3]$ ./hash-table-tester -t 4 -s 50000
Generation: 34,255 usec
Hash table base: 207,139 usec
  - 0 missing
Hash table v1: 455,748 usec
  - 0 missing
Hash table v2: 132,790 usec
  - 0 missing


## First Implementation
In the `hash_table_v1_add_entry` function, I utilize a single mutex that ensures synchronization between all threads when adding an entry: by locking the mutex before entering the logic of the function, any other thread will be put to sleep that tries to add an entry to the hash table if a thread is already doing so. This prevents race conditions by ensuring no two threads are doing a write to shared memory. The mutex is then unlocked at the end of all the logic to allow other threads to run.

### Performance
```shell
TODO how to run and results
```
Version 1 is alot slower than base because AT MOST 1 thread can add an entry to the hash table at once, so multi-threading is not being utilized. However, in the base implementation, entries are added serially which prevents any overhead (also, context-switching if OS needs to switch from a Thread that is blocked by a mutex to a thread that has acquired the mutex) and lock contention associated with mutexes and synchronization. If multi-threading is properly utilized, it gives better performance than a serial implementation but in the case of Version 1, only a singular thread can be put to work at once. 

## Second Implementation
In the `hash_table_v2_add_entry` function, I added a mutex to the hash_table_entry struct; thus, each entry in the hash table will maintain its own lock so multi-threading will actually be utilized since multiple threads can work on different entries, while two threads cannot work on the same entry. Further, locks are only placed around writes to shared memory (updating existing list entry or inserting list_entry if it does not exist) to minimize lock contention and ensure that threads are not waiting unneccesarily long to do operations. Furthermore, this implementation of locking will give better performance for a larger number of entries, wheras a smaller number of entries might cause more lock contention since two threads are more likely to "fight" over a lock for a singular entry. 

### Performance
```shell
TODO how to run and results
```
[classvti@lnxsrv13 ~/Desktop/lab3]$ ./hash-table-tester -t 4 -s 50000
Generation: 34,899 usec
Hash table base: 199,950 usec
  - 0 missing
Hash table v1: 430,261 usec
  - 0 missing
Hash table v2: 62,618 usec
  - 0 missing
  
We should expect a 3x speedup between the base implementation and V2 since we added 3 extra threads. Based on the results, we observe what we expected: v2 is 3.19 faster than base. 

## Cleaning up
```shell
TODO how to clean
```
run 'make clean'
