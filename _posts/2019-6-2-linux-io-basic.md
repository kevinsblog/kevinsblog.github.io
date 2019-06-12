---
layout: post
title: Linux I\O总结 -- 基础概念篇
---

## 文件描述符
##### 0, 1, 2 文件描述符(fd)

- 0 is standard in (stdin),1 is standard out (stdout), and file descriptor 2 is standard error (stderr).
- STDIN_FILENO, STDOUT_FILENO, and STDERR_FILENO in C std lib, by default, keyboard, terminal's display

##### fd与文件

- fd refer to regular file, device files, pips

##### 进程文件表

- a process's file table, contains open files and their access mode, current file position, and other metadata, a child process receives a copy of its parent's file table, which means file operations in one process doesn't affect the other. 

##### fd重定向

- redirect the std fd, pipe the output of one program into another

## 新创建的文件
##### 文件的用户访问权限

- owner user is the uid of process creating the file
- owner group could be the gid of process creating file, or maybe the gid of the parent directory

##### 设置文件访问权限，读写和执行

- file access permission can be set through mode bitset
- by default '0644', owner can read and write, everyone else can only read
- '0700' executable by owner
- permission is a process-specific attribute set via login shell

## 写文件的过程
##### 文件推迟写

- when call of write() returns, the kernel has copied the data from supplied buffer to a kernel buffer, however, there is no guarantee that data would be written to its destination (system calls return much faster than this happens due to the disparity in performance between processor and hard disk)

##### 内核回写文件

- kernel gathers up all the dirty buffers (buffers that contain data newer than what is on disk), sort them optimally and writes them to the disk (so called writeback), therefore, kernel actually defer writes to more idle periods and batch many writes together (delayed writes)

##### 内核读缓存

- when read is issued for a piece of just-written data that only lives in buffer (in-memory cache) rather than disk, that request will directly satisfied from the buffer and not cause read from disk. However, this is just the results as expected, if system does not crash before data makes it to disk.

##### 内核写顺序

- write ordering may take cared by application and kernel will reorder the write requests asit sees fit(for reason of performance), all request would hit the disk. the ordering for database is a concern, they need to ensure the write order, therefore, database file write need to be sync

##### I/O错误

- I/O errors, I/O errors occur during writeback have no chance to be reported back to the issuing process. Actually, dirty buffers inside kernel are not associated with processes.Multiple processes may dirty a single buffer. 

##### 内核回写规则

- Kernel attempts to write out data in a timely manner, which is by 'maximum buffer age', specified in centiseconds, 0.01s manner.

## 读文件的过程
##### 发出读指令

- C lib provides definitions of sys call that would be converted to trap statements at compile time.

##### 读系统调用

- user-space process is trapped into the kernel, pass through the sys call handler and handed to the read() sys call.
- kernel figures out what obj backs the given fd, then invokes the read func associated with backing obj.
- read func is part of file sys code, who does the physical readng from the file sys, return data to user-space read() call, then returns sys call handler, and copies data back to user space.
- sys call rets and process continues to exec

## append写
##### 消除潜在的竞争，自动设置文件位置

- mulitple process may race for the end of the same log file without synchronization
- append mode avoid this race condtion and ensures the file pos is always set to the end of the file in all writes, file pos is automatically updated before new read() issues.

## 同步写操作

- buffering writes provide a significant performance improvement, but application may need to ctrl when data hits the disk, at this case, performance need to traded fro synchronized operations.

## 直接I/O

- bypass the complex layer of cache, buffering and I/O management between devices and applications and perform app's own I/O management.
- e.g. database systems often prefer to perform their own caching and want to minimize the presentce of OS.
- flag O_DIRECT in open() will instructs the kernel minimize I\O managment, I\O will initiate directly from user-space buffer to device, all I\O will be sync
- the request length, buffer alignment and file offset must be integer multiples of underlying device sector size.

## 多路复用I/O

- application often need to block on more than one fd, e.g keyboard(stdin), ipc, files, GUI application may contend with hundreds of pending event in mainloop.
- thread can servicing each fd separately,but one process cannot block on more than one fd at the same time, say, if a read() system call is issued and data is not yet, process will block, no longer able to service the other fd.
- with nonblocking I/O, application can issue I/O requests that return a special error code instead of blocking, but this is inefficient since first, process needs to continually issue I/O, waiting fro one of its fd to be ready (poor design), secondly, application can not sleep, free the processor for other tasks.
- multiplexed I/O allows application to concurrently block on multiple fd, and receive notification when any one of fd becomes ready without blocking.
- work procedure of multiplexed I/O is like,
1. issue multiplexed I/O: tell process any of fd is ready for I/O
2. sleep until one or more fd is ready
3. wake up process
4. handle all fd that is ready
5. go back to sleep

## I/O的内核内幕

- VFS is a mechanism of abstraction that allows kernel to call file sys functions and manipulate file sys data without caring about the specific file sys type, so call 'common file model'.
- VFS is implemented via function pointer and object-oriented practices, the 'common model' provides a framework to which file sys in kernel must adhere. 
- VFS framework provides hooks to support reading, creating links, synchronizing and so on.
- VFS framework enable sys call read/write from/to any medium. All file sys support the same concepts, same interfaces and sys calls.
- Page cache is a in-memory store of recently accessed data from on-disk file sys, which faster the access speed and avoiding repeated disk access.
- Page cache exploits the concept of 'temporal locality', resouce accessed at one point are very likely to be accessed again in the near future.
- Page cache is the first place for kernel looks for file sys data. data is transferred from disk to cache on first time visit, later access would simply return from cache. cache operations are transparently and ensuring its data is always valid.
- Page cache is dynamic in size. I\O brings more and more data, with page cache size grows, consuming any free memory. If page cache eventually consumed all free memory, page cahce will be 'pruned', releasing the least-used pages to make room.
- Often kernel try to store data on the disk to have a larger memory foorprint, which is swap to disk a seldom-used page of process memory.
- 'prune' and 'swap' preference can be adjust via /proc/sys/vm/swappiness
- Another locality of reference is 'sequential locality', which says data is often reference sequentially. kernel provides 'page cache readahed' to take advantage of this. Reading extra data off the disk and into page cache on read request.
- Kernel handle readahead dynamicly, if it's noticed that a process is consistently using data readaheaded, kernel may enlarge the readahead window from 16 KB to 128 KB
- Page writeback is that kernel defers writes via buffers. Eventually, dirty buffers is committed to disk, synchronizing on-disk file with data in memory
- Dirty buffers are written when, free memory shrinks below a configurable threshold or a dirty buffer ages beyond a configurable threshold
- Kernel carry out writeback by 'flusher' threads, flusher will wake up and begin to commit dirty buffers to disk when above condition is true
- Buffers in kernel represented by buffer_head structure, which tracks various metadata associated with buffer
- The buffer data resides in page cache

## 相关的API(系统调用)

```c
#include <sys/types.h>
#include <sys/stat.h>
#include <fcntl.h>
//maps the file (pathname) to a fd, file pos is to the start of file, access mode is also specified
/*
flags can be combined in bitwise-or manner
O_RDONLY, O_WRONLY, or O_RDWR
O_APPEND: file pos is being updated to the end of file instead (always synchorize to the real last pos after last writes(even from another process))
O_ASYNC: a signal (e.g. SIGIO) generated when file becomes readable/writeable, only for FIFOs, pipes, sockets and terminals
O_CLOEXEC: automatically close file upon executing a new process, which is for eliminates a race condition
O_CREAT: create the file if not exist, otherwise no effect
O_DIRECTORY: target is a directory, to support opendir()
O_EXCL: cause O_CREAT to fail if file already exists, which is used to prevent race conditions on file creation
O_LARGEFILE: target files is larger than 2GB, open file using 64-bit offset for file pos
O_NONBLOCK: open file in nonblocking mode, no operations will cause process to block on I/O, it's defined for FIFOs
O_SYNC: open file for synchronous I\O, write operation will not complete until data has been physically written to disk, no effect on reads since they are already synchronous
O_TRUNC: for regular file, file will be truncated to zero length once open, do not use O_TRUNC | O_RDONLY, it's undefined
*/
int open (const char *name, int flags);
```
```c
/*
    owner - S_IRWXU (RWX)  S_IRUSR (R-only) S_IWUSR(W-only)  S_IXUSR(execute only)
    group - S_IRWXG        S_IRGRP          S_IWGRP          S_IXGRP
    others - S_IRWXO       S_IROTH          S_IWOTH          S_IXOTH
*/
int open (const char *name, int flags, mode_t mode);
//shorcut for open in O_WRONLY | O_CREAT | O_TRUNC
//return fd on success, on error, return -1 and set errno
int creat (const char *name, mode_t mode);
```
```c
#include <unistd.h>
/*reads up to len bytes from current file   pos into memory pointed by buf, return the number of bytes read, the file pos is also advanced, return
  - size may less than len, maybe less than len bytes may available, or sys call have been interrupted by a signal, pipe broken
  - ret size may be 0 to indicate EOF(end of file), in case of blocking, read is waiting for more data (socket or device file)*/
ssize_t read (int fd, void *buf, size_t len);
```

```c
//read all bytes with error handled (parital reads)
/*
   EBADF, EINVAL(bad fd), EFAULT(bad read buf),  EIO
*/
ssize_t ret; //limited by SSIZE_MAX 2^31
if (len > SSIZE_MAX)
    len = SSIZE_MAX;

while (len != 0 && (ret = read(fd, buf, len)) != 0) //read until EOF reached
{
    if (ret == −1)
    {
        if (errno == EINTR) //reissue the call
            continue;
        if (errno == EAGAIN) //non-blocking reads, we may want call to read ret immediately,
                             // indicating no data available
            //resubmit later
        perror("read");
        break;
    }
    len -= ret; //to handling partial reading
    buf += ret;
}
```
```c
/*writes up to count bytes starting at buf to current file pos, for files not support seeking (character device), always from 'head' ret the num of bytes written */
/*
regular files is guaranteed to not perform partial writes,for other file, e.g. socket, it may happens
*/
ssize_t write (int fd, const void *buf, size_t count);
```
```c
ssize_t ret, nr;
while (len != 0 && (ret = write(fd, buf, len)) != 0)
{
    if (ret == −1)
    {
        if (errno == EINTR)
            continue;
        perror("write");
        break;
    }
    len -= ret;
    buf += ret;
}
```
```c
/* ensure all dirty data and metadata (creation timestamp, attributes) associated with file are written back to disk, function call will not return until disk says data are reached */
//write back data, update inode's data, where expensive seek operation may happens
int fsync (int fd);
/*only flushes data and essential data when access file (e.g. file size), which is potentially faster, nonessential data (modification timestamp) is not guaranteed to synchronized */
int fdatasync (int fd);
//all buffers—both data and metadata—are guaranteed to reside on disk
void sync (void);
//setting sync flag
//O_DSYNC: sync only normal data  O_RSYNC
fd = open (file, O_WRONLY | O_SYNC);
```
```c
//unmap fd, invalid fd and ret to OS, closing a file does not bearing that file is flushed
//  to disk, sync ensure that file is committed to disk 
//when the last open fd referring to a file is closed, the data structure representing the file
//  inside kernel is freed, and it unpins the in-memory copy of the file inode, if nothing else
//  is pinning the inode, inode may too freed in memory
//if a file has been unlinked from disk but kept open before it unlinked, the file is not physically
//  removed until file closed and inode removed from memory. Therefore, close() can result in unlinked
//  file finally being physically removed from disk
int close (int fd);
```
```c
//set the file pos of a fd to a given value and jump around in a file
/*
    origin - start origin point of setting
    SEEK_CUR, cur file pos + pos
    SEEK_END, end file pos + pos
    SEEK_SET, pos (absolute pos)
*/
//ret the new file pos
off_t lseek (int fd, off_t pos, int origin);

//get current file pos
int pos;
pos = lseek (fd, 0, SEEK_CUR);

//set file pos to the end of file
off_t ret;
ret = lseek (fd, 0, SEEK_END);
if (ret == (off_t) −1)
    return -1;

//set with a absolute file pos
off_t ret;
ret = lseek(fd, (off_t)1825, SEEK_SET);
if (ret == (off_t) −1)
    return -1;
//seeking past the end of the file
//  -a read request to the newly created file pos will ret EOF
//  -write request to this pos, new space will be created between old length and new length, 
//      and padded with zeros
//  -zero padding makes a hole, a hole on Unix-style filesystem do not occupy any physical
//      disk space (which implies the total size of files can add up to more than the physical
//      disk size)
//  -files with holes called sparse files, sparse file can enhance performance since manipulating
//      holes doesn't initiate physical IO
//

int ret;
//size of off_t is often C long type, which is generally the word size (the size of
//  machine's general-propose registers), 32-bit machine may encounter EOVERFLOW errors
ret = lseek (fd, (off_t) 1688, SEEK_END);
if (ret == (off_t) −1)
    return -1;
```

```c
//not like read() and write(), pread() and pwrite() do not update the file position
//  upon completion, they completely ignore the current file position
//they may enable caller to do some tricky operations such as moving file backward or
//  randomly, and avoid any potential races occur when using lseek()
ssize_t pread (int fd, void *buf, size_t count, off_t pos);
ssize_t pwrite (int fd, const void *buf, size_t count, off_t pos);
```

```c
//two sys calls for truncating the len of file (smaller or larger), file must be writable
int ftruncate (int fd, off_t len);
int truncate (const char *path, off_t len);
```

```c
#include <sys/select.h>
#include <sys/time.h>
struct timeval
{
    long tv_sec;  /* seconds */
    long tv_usec; /* microseconds */
};
//a call to select() blocks until the given fd are ready for I/O, or until some timeout
/*
    n: value of highest-valued fd in any set plus one
    timeout: specify the return timeout
*/
/*
    three sets to watch (set could be NULL)
        readfds: watched to see if data is available for reading
        writefds: ... writing
        exceptfds: ... if exception occurred, or if out-of-band data available (only for sockets)
*/
int select(int n,
           fd_set *readfds,
           fd_set *writefds,
           fd_set *exceptfds,
           struct timeval *timeout);
//manage the fd sets
FD_CLR(int fd, fd_set *set);//remove a fd from the set
FD_ISSET(int fd, fd_set *set);//test whether a fd is part of the set
FD_SET(int fd, fd_set *set);//add a fd to the set
FD_ZERO(fd_set *set); //remove all fd from the set
```

```c
//apply select() to sleep for subsecond-resolution time
struct timeval tv;
tv.tv_sec = 0;
tv.tv_usec = 500;
/* sleep for 500 microseconds */
select(0, NULL, NULL, NULL, &tv);
```

```c
//System V’s multiplexed I/O solution
#include <poll.h>
struct pollfd
{
    int fd;        /* file descriptor */
    short events;  /* requested events to watch */
    short revents; /* returned events witnessed */
};
/*
  fds: each pollfd structure specifies a single fd to watch
  events: POLLIN, POLLOUT,...
*/
int poll (struct pollfd *fds, nfds_t nfds, int timeout);

struct pollfd fds[2];
int ret;
/* watch stdin for input */
fds[0].fd = STDIN_FILENO;
fds[0].events = POLLIN;
/* watch stdout for ability to write (almost always true) */
fds[1].fd = STDOUT_FILENO;
fds[1].events = POLLOUT;
/* All set, block! */
ret = poll(fds, 2, TIMEOUT * 1000);
if (ret == −1)
{
    perror("poll");
    return 1;
}
```