---
layout: post
title: Linux I\O Summary -- Buffered I\O
tags: [Linux]
bigimg: /img/path.jpg
comments: true
---

## Block

Block is an abstraction representing the smallest unit of storage on file system. Generally, no I/O operation can execute on amount of data less than the block size or not an interger multiple of the block size.

Processor handle I/O aligned on block. Non block-aligned I/O is inefficient, need to round up to next largest block.

## Data Alignment

Processors do not read and write from memory in byte-sized chunks, but with specific granularity, such as 2, 4, 8 or 16 bytes. Therefore, processors initiate access from address that is an integer multiple of granularity.

Meanwhile, C variables must be stored at and access from aligned address. a 32-bit integer is aligned on a 4-byte boundary.

Access misaligned data has penalties, some processor can access misaligned data with performance penalty, some cannot at all, and access would cause hardware exception. Worsely, some processors sliently drop low-order bits to force address be aligned, causing unintended behavior.

Alignment are naturally handled by compiler. But when saving binary data, dealing structure, and communicating over network bring alignment issues to the forefront.

## Fields and Strings

Although processor talks on block, application operate in terms of high-level abstractions such as fields or strings. 

The size of fields varies, at its worst, user-space application might read and write a single byte at a time.

## User-buffered I/O

Application wants to read/write data in way feel natural, while the actual I/O occur in units of file sys block size.

Therefore, user-buffered I/O is introduced. when data is written, it is stored in a buffer inside program's address space, when buffer reaches a set size (buffer size), the entire buffer is written out in a single write().

Likewise, when application issues read requests, requests are not served directly by file system, but via the chunks of buffer. Data in buffer is hand out piece by piece. When buffer is empty, another large block-aligned chunk would be read in.

The user-buffer is designed to help application handle I\O in a natrual, reading and writing data in the unit of field. While only issue large, block-aligned reads and writes.

Kernel also buffers data for delaying writes or read ahead, but this has nothing to do with user buffer. 

## C Standard Library I\O (stdio)

Developer can choose to use stdio, home-rolled user-buffer solution or straight sys calls when handling I\O.

Stdio operate on unique identifier, file pointer(fp) rather than directly on file descriptor(fd). While fp is represented by a pointer to FILE typedef. And opened file is called a stream, input streams or output streams

## Type of buffering in stdio

##### Unbuffered

where data directly submit to the kernel, stderr is by default unbuffered.

##### Line-buffered

where buffering performed on per-line basis, with newline character, buffer is submitted to the kernel. it makes sense for streams being output to screen, stdout is by default line-buffered.

##### Block-buffered

where buffering is on per-block basis, ideal for files, all streams associated with files are by default block buffering.

## Thread-safty of stdio

I/O functions are thread-safe, they associate a lock, a lock count and a owning thread, on each open stream.

Any given thread must acquire the lock and become the owning thread before issuing any I/O requests. Even if two threads each issue a write rquest at the same time, locking will ensure one write completes before the other. But if the requests are from different process, maunally file locking is necessary.

## stdio API

##### Open file given behavior mode

The stream postion of newly opened file will be put at the start of file. modes canbe specified by input mode string.

| Mode     |      Stream                 |  Pos  |
|----------|:----------------------:-----|------:|
| r        | read                        | nxt   |
| r+       | read + write                | nxt   |
| w        | write, empty file or create | beg   |
| w        | w + read                    | beg   |
| a        | write append, create        | end   |
| a        | write append + read, create | end   |
| b        | non-unix  binary mode       | N.A.  |

```c
/*
    position stream at start of file -
    r (read), r+ (read + write), w (write, truncated file to 0 if file exists, create one if not exists)
    w+ (write + read, include behavior of w)
    position stream at end of file -
    a (wirte in append mode, create if not exists), a+ (append read + write)
    b (on windows, open file in binary mode)
*/
FILE * fopen (const char *path, const char *mode);
```
Convert a fd to a stram can be useful when you need to call stdio functions as well as system call functions.
```c
FILE * fdopen (int fd, const char *mode);
```

##### Close file stream

Any buffered and not-yet-written data is flushed when closing the stream.
```c
int fclose (FILE *stream);
```
One is also able to close all streams associated with current process, including stdin, stdout and stderr.
```c
int fcloseall (void);
```

##### Read from file stream

Read one character and return as 'int'. Since the API need to commuicate errors with caller, ther return 'unsigned char' is cast to 'int'. Meanwhile, stream position move by one
//  after read
```c
int fgetc (FILE *stream);
```

One example of read stream is
```c
int c = fgetc (stream);
if (c == EOF){
    printf("nok");
}else{
    printf("read %c", (char)c);
}
```

Read a string from stream, reads up to one less than size bytes from stream, on success, str returned, on failure, NULL is returned
```c
char * fgets (char *str, int size, FILE *stream);
```
One example of read a string from stream is,
```c
char buf[LINE_MAX];
if (!fgets (buf, LINE_MAX, stream)){

}
```

Read binary data, such as C structures, read up tp nr elements of data (each of size bytes) from stream into buf, fp, return the num of elements read (not number of bytes) also advanced by num of bytes. Binary data diff in variable size, alignment, padding and byte order, therefore, you cannot
expect to read binary data created in different platform.
```c
size_t fread(void *buf, size_t size, size_t nr, FILE *stream);
```
for example,
```c
char buf[64];
size_t nr;
nr = fread(buf, sizeof(buf), 1, stream);
if (nr == 0)
{
    /* error*/
}
```
##### Write to file stream

Put a character to stream is just like read,
```c
// write a char to the stream, return c if success, otherwise EOF
int fputc (int c, FILE *stream);
// write all of the null-terminated string to stream, on failure return EOF
int fputs (const char *str, FILE *stream);
// write to stream up to nr elements, each size bytes in length
// advance fp, return num of elements write
size_t fwrite(void *buf,
              size_t size,
              size_t nr,
              FILE *stream);
```

One example of read stream until reach EOF or a delimiter is like,
```c
char *s;
int c = 0;
s = str; //str point to a buffer
while (--n > 0 && (c = fgetc(stream)) != EOF && (*s++ = c) != '\n')
    ;
if (c == d)
    *--s = '\0';
else
    *s = '\0';
```

### Seek and change fp position
Manipulate the file pos of stream with offset can work like,
    - SEEK_SET: file pos set to be offset
    - SEEK_CUR: file pos set to be current pos + offset
    - SEEK_END: file pos set to be end of file + offset
```c
int fseek (FILE *stream, long offset, int whence);
```

one can simply set stream poition with an absolute offset,
```c
int fsetpos (FILE *stream, fpos_t *pos);
```
set to the start of file
```c
void rewind (FILE *stream);
```
return the current stream position, return -1 on error
```c
long ftell (FILE *stream);
int fgetpos (FILE *stream, fpos_t *pos);
```

### Other APIs
Write out the user buffer to the kernel, if stream is NULL, all open input stream is flushed, it does not guarantee that data is committed to any medium, need
to call fsync().
```c
int fflush (FILE *stream);
```
Stdio function is poor on communicating error with caller, therefore, we can take below function to test
error,
```c
// tests whether the error indicator is set, return nonzero error value if occurred error
int ferror (FILE *stream);
// return nonzero value if EOF reached (EOF indicator)
int feof (FILE *stream);
// clears error and EOF indicator
void clearerr (FILE *stream);
```
Caller can get fd that binding with fp.
```c
int fileno (FILE *stream);
```
The buffer type can also be set through,
```c
/*
    mode: _IONBF(unbuffered), _IOLBF(line-buffered), _IOFBF(block-buffered)
    buf of size bytes will be employed by stdio as buffer for the given stream
    do not supply the buffer as automatic variable in a scope (not to provide a buffer local 
        to main())
*/
int setvbuf (FILE *stream, char *buf, int mode, size_t size);
```

### Manual file lock

When multiple threads will submitting I/O to the same stream, you need an atomic region greater than a single function.

flockfile() waits until stream is no longer locked, then increase the lock count and acquire the lock, 
becoming the owning thread of the stream.

funlockfile() decrements the lock count associated with stream, and if the lock count reaches zero, the current thread relinquishes ownership of the stream.

the call of lock() can be nested.
```c
void flockfile (FILE *stream);
void funlockfile (FILE *stream); //decrements the lock count associated with stream
```
A example of write out several lines to file is like
```c
flockfile (stream);
fputs ("List of treasure:\n", stream);
fputs (" (1) 500 gold coins\n", stream);
fputs (" (2) Wonderfully ornate dishware\n", stream);
funlockfile (stream);
```

A nonblocking version of flockfile() is
```c
int ftrylockfile (FILE *stream);
```
if the stream is currently locked, ftrylockfile() do nothing and returns a non-zero value.

It might be possible to minimize the overhead of locking and to improve performance.
```c
int fgetc_unlocked (FILE *stream);
char *fgets_unlocked (char *str, int size, FILE *stream);
size_t fread_unlocked (void *buf, size_t size, size_t nr,
FILE *stream);
int fputc_unlocked (int c, FILE *stream);
int fputs_unlocked (const char *str, FILE *stream);
size_t fwrite_unlocked (void *buf, size_t size, size_t nr,
    FILE *stream);
int fflush_unlocked (FILE *stream);
int feof_unlocked (FILE *stream);
int ferror_unlocked (FILE *stream);
int fileno_unlocked (FILE *stream);
void clearerr_unlocked (FILE *stream);
```
