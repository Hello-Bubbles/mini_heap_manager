Mini Heap Manager (Embedded malloc/free)

A lightweight custom heap allocator implemented in C for learning and experimentation.
This project demonstrates how malloc/free work internally using a static heap,
block headers, splitting, and coalescing, with attention to alignment and safety checks.

Designed primarily for embedded systems learning, but builds and runs on desktop as well.


FEATURES
--------
- Static heap (no dependency on system malloc)
- First-fit allocation strategy
- Block splitting on allocation
- Coalescing of adjacent free blocks on free
- 8-byte alignment guarantee (safe for double, uint64_t, structs)
- Pointer validation before free (range + alignment checks)
- Debug helper to visualize heap state


MEMORY LAYOUT
-------------
Each heap block has the following layout:

    +------------------+
    | Block Header     |
    |------------------|
    | Payload (user)   |
    +------------------+

The header stores:
- payload size
- free/used flag
- pointer to next block

All blocks are maintained in a single linked list in address order.


DESIGN CHOICES
--------------
- Allocation strategy: First-fit
- Heap structure: Single list containing both used and free blocks
- Alignment:
  - Heap base aligned to 8 bytes
  - Payload size aligned to 8 bytes
- Splitting rule:
  A block is split only if the remainder can hold:
      sizeof(header) + MIN_PAYLOAD
- Coalescing:
  Adjacent free blocks are merged during minifree()


DEBUGGING SUPPORT
-----------------
heap_dump() prints the current heap state, showing:
- block index
- header address
- payload address
- block size
- FREE / USED state
- next pointer

This helps visualize fragmentation, splitting, and merging.


SAFETY CHECKS
-------------
Before freeing a pointer:
- Pointer must lie within heap range
- Pointer must be correctly aligned (8-byte)
- Basic size sanity checks are applied

Invalid frees are ignored to prevent heap corruption.


PROJECT STRUCTURE
-----------------
mini-heap-manager/
  heap.c     allocator implementation
  heap.h     public API
  main.c     test/demo program
  README.txt this file


BUILD AND RUN
-------------
Linux / MinGW:

    gcc -Wall -Wextra -O0 -g heap.c main.c -o mini_heap
    ./mini_heap

## What I learned / bugs I hit:
* How real allocators represent memory using headers + payload
* Why alignment matters (and how misalignment breaks structs/doubles)
* Fragmentation patterns and why coalescing is essential
* Splitting edge cases (remainder too small to be useful)
* Pointer validation importance to avoid heap corruption
* Why allocation/free cost becomes O(n) with a single list

## Limitations:

* Learning allocator, not production-grade
* Not thread-safe / ISR-safe
* No double-free detection
* No best-fit / segregated free lists
* No heap expansion
* O(n) traversal cost on allocation/free

## Future improvements:
* Double-free detection (magic numbers / block state validation)
* Heap statistics:
* total free bytes
* largest free block
* fragmentation ratio
* Best-fit or next-fit strategies
* Separate free list to speed allocations
* Optional “poison fill” on free to catch use-after-free
* Thread-safe version (mutex / critical section wrappers)


