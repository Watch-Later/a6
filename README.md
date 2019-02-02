# liba6 - linux-based user-thread executor library

|CI           |Build on OS  |Host OS      |Build Status     |
|:------------|:------------|:------------|:----------------|
|**Travis CI**|Ubuntu Trusty|Ubuntu Trusty|[![Build Status](https://travis-ci.org/Alpacius/a6.svg?branch=master)](https://travis-ci.org/Alpacius/a6)|

|Compiler     |Context Switch Implementation  |Comments     |
|:------------|:------------------------------|:------------|
|**GCC 4.9**  |POSIX ucontext                 |--           |
|**GCC 5**    |POSIX ucontext                 |--           |
|**GCC 6**    |POSIX ucontext                 |--           |
|**GCC 7**    |POSIX ucontext                 |--           |
|**GCC 8**    |POSIX ucontext                 |--           |
|**GCC 4.9**  |X86-64                         |--           |
|**GCC 8**    |X86-64                         |--           |

Liba6 is a lightweight user-level thread library, playing a role as an alternative to 
traditional thread pool solution for concurrent I/O intensive tasks.

Currently, liba6 is only supported on Linux due to platform-specified implementation of I/O poller.

## Build prerequisites
* Linux >= 2.5 (for epoll APIs)
* GCC >= 4.9 (for `__auto_type` extension)

## Features
* Stackful user-level threads.
* Barrier-based semi-auto thread switch APIs for blocking & pollable fd-based I/O.
* Thread-pool-styled APIs for user-level thread management.
* Fast context switch under x86-64.

## Further Works
* Timeouts on I/O barriers.
* Promise API for blocking & non-pollable external operations.

## May-be Goals
* User-level lock support.
* Cancellation points.
* Limited preemptive rescheduling.

## Non-goals
* Interthread communication support (e.g. channels, mailboxes).

## Build
For build:
```
mkdir -p m4
autoreconf -ifv
./configure   # optional feature --enable-arch-specified-impl for optimized implementation (currently available for `uname -m` = "x86_64" only)
make   # or make check for test (the same test suites on CI platforms)
```

For install of libraries (both archive and shared library):
```
make install
```

## Examples
A simple example:
```C
// simple_demo.c

#include    <stdio.h>
#include    <stdlib.h>
#include    <string.h>
#include    <unistd.h>
#include    <sys/types.h>
#include    <errno.h>

#include    <a6/swarm.h>

int barrier = 1;

void func(void *arg) {
    const char *uth_name = arg;
    a6_read_barrier_oneshot(STDIN_FILENO, 0);
    char inbuf[64];
    ssize_t n_bytes_read = read(STDIN_FILENO, inbuf, 64);
    if (n_bytes_read == -1) {
        perror("read");
        __atomic_store_n(&barrier, 0, __ATOMIC_RELEASE);
    } else {
        a6_write_barrier_oneshot(STDOUT_FILENO, 0);
        dprintf(STDOUT_FILENO, "%s says: %s\n", uth_name, inbuf);
    }
}

int main(void) {
    struct a6_swarm *swarm = a6_swarm_create(2);
    a6_swarm_launch(swarm);
    a6_swarm_run(swarm, func, "uthread-1");
    while (__atomic_load_n(&barrier, __ATOMIC_ACQUIRE));
    getchar();
    a6_swarm_destroy(swarm);
    return 0;
}
```

Compile with:
```
$ gcc -o simple_demo simple_demo.c -la6
```

Run with:
```
[user@host demo]$ ./simple_demo 
aaaa
uthread-1 says: aaaa


[user@host demo]$ 
```

See [tests](https://github.com/Alpacius/a6/tree/master/test) for more examples.

## API Manual

### User-level thread pool (swarm) management 

#### Construct & Destruct

##### Synopsis
```C
#include    <a6/swarm.h>

struct a6_swarm *a6_swarm_create(uint32_t size);
int a6_swarm_launch(struct a6_swarm *swarm);

void a6_swarm_destroy(struct a6_swarm *swarm);
```

##### Description
```
// TODO implementation
```

##### Return Value
```
// TODO implementation
```

##### Thread Safety
```
// TODO Safe race:swarm
```

#

#### Launch a user-level thread

##### Synopsis
```C
#include    <a6/swarm.h>

int a6_swarm_run(struct a6_swarm *swarm, void (*func)(void *), void *arg);
```

##### Description
```
// TODO implementation
```

##### Return Value
```
// TODO implementation
```

##### Thread Safety
```
// TODO Safe race:swarm
```

#

#### I/O barriers

##### Synopsis
```C
#include    <a6/swarm.h>

int a6_read_barrier_oneshot(int fd, uint32_t options);
int a6_write_barrier_oneshot(int fd, uint32_t options);
```

##### Description
```
// TODO implementation
```

##### Return Value
```
// TODO implementation
```

##### Thread Safety
```
// TODO Safe
```
