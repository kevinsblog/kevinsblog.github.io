---
layout: post
title: APUE笔记
tags: [Leetcode, C]
bigimg: /img/path.jpg
comments: true
---

* toc
{:toc}

# 参考站点

[open group](https://pubs.opengroup.org/onlinepubs)

# 使用的shell命令

```shell
ls -l
grep -r 'err_quit' .
```

```
# dump in c-style character
od -c file.hole
```

退出程序
ctrl + d

# 可变长参数列表

A function may be called with a varying number of arguments of
    varying types.  The include file <stdarg.h> declares a type va_list
    and defines three macros for stepping through a list of arguments
    whose number and types are not known to the called function.

The called function must declare an object of type va_list which is
    used by the macros va_start(), va_arg(), and va_end().

The va_start() macro initializes ap for subsequent use by va_arg()
    and va_end()

The va_arg() macro expands to an expression that has the type and
    value of the next argument in the call

Each invocation of va_start() must be matched by a corresponding
    invocation of va_end() in the same function

{% highlight c linenos %}
void PrintFloats (int n, ...){
    int i;
    double val;
    printf ("Printing floats:");
    va_list vl; //build var arg list
    va_start(vl,n); //beg of var arg list op
    for (i=0; i < n; i++){
        val = va_arg(vl,double); //expand arg by tyoe
        printf (" [%.2f]",val);
    }
    va_end(vl); //end of var arg list op
    printf ("\n");
}
...

    PrintFloats(3,3.14159,2.71828,1.41421);
{% endhighlight %}

# 错误处理

程序运行中出现无法逆转、规避的错误时，需要打印错误信息并退出。

{% highlight c linenos %}
/*
 * Fatal error unrelated to a system call.
 * Print a message and terminate.
 */
void
err_quit(const char *fmt, ...)
{
	va_list		ap;

	va_start(ap, fmt);
	err_doit(0, 0, fmt, ap);
	va_end(ap);
	exit(1); //exit with err code
}

/*
 * Print a message and return to caller.
 * Caller specifies "errnoflag".
 */
static void
err_doit(int errnoflag, int error, const char *fmt, va_list ap)
{
	char	buf[MAXLINE];

	//Write formatted data from variable argument list to sized buffer
	vsnprintf(buf, MAXLINE-1, fmt, ap);
	//printf sys error msg to buf
	if (errnoflag)
		snprintf(buf+strlen(buf), MAXLINE-strlen(buf)-1, ": %s",
		  strerror(error));
	//add new line
	strcat(buf, "\n");
	fflush(stdout);		/* in case stdout and stderr are the same */
	fputs(buf, stderr);
	fflush(NULL);		/* flushes all stdio output streams */
}
{% endhighlight %}

调用时，
```c
	if (argc != 2)
		err_quit("usage: ls directory_name");
```

# ls的一种简单实现

```c
#include <dirent.h> /*format of directory entries*/
```
{% highlight c linenos %}
	/*a pointer to be used to identify
     the directory stream */
	DIR				*dp; 
	/* */
	struct dirent	*dirp; /*directory entry for traversal*/

	if (argc != 2)
		err_quit("usage: ls directory_name");

	/*opens the directory named by filename */
	if ((dp = opendir(argv[1])) == NULL)
		err_sys("can't open %s", argv[1]);

	/*returns a pointer to the next directory entry */
	int cnt = 0;
	while ((dirp = readdir(dp)) != NULL){
		cnt++;
		printf("[%d]%s\n", cnt, dirp->d_name);	
	}

	/*closes the named directory stream and frees the
     structure associated with the dirp pointer */
	closedir(dp);
{% endhighlight %}
测试一下，列举当前目录下的所有文件。
```
KevinLiu$ ./ls1 ./
[1].
[2]..
[3]getcputc
[4]testerror.c
```

# cat的一种简单实现

buffered实现
{% highlight c linenos %}
	int		n;
	char	buf[BUFFSIZE];

	/*read from std input and write to std output */
	while ((n = read(STDIN_FILENO, buf, BUFFSIZE)) > 0)
		if (write(STDOUT_FILENO, buf, n) != n)
			err_sys("write error");

	if (n < 0)
		err_sys("read error");
{% endhighlight %}

重定向，
```shell
# redirect stdout to tmp
$ ./mycat > tmp 
test
# redirect stdin to tmp, print content of tmp
$ ./mycat < tmp
test
# redirect stdin and stdout, copy content of tmp to tmp_out
$ ./mycat < tmp > tmp_out
```

unbuffered实现
{% highlight c linenos %}
	int		c;

	/* reads one character at a time */
	while ((c = getc(stdin)) != EOF)
		if (putc(c, stdout) == EOF) /* write one char at a time */
			err_sys("output error");

	if (ferror(stdin))
		err_sys("input error");
{% endhighlight %}

# 自制简单的shell

{% highlight c linenos %}
	char	buf[MAXLINE];	/* from apue.h */
	pid_t	pid;
	int		status;

	printf("%% ");	/* print prompt (printf requires %% to print %) */
	while (fgets(buf, MAXLINE, stdin) != NULL) { /* read one line at a time */
		/* each line returned from fgets is terminated with \n */
		if (buf[strlen(buf) - 1] == '\n')
			buf[strlen(buf) - 1] = 0; /* replace newline with null */

		if ((pid = fork()) < 0) { /*create new process */
			err_sys("fork error");
		} else if (pid == 0) {		/* child */
			/* exec cmd and replace the child process */
			execlp(buf, buf, (char *)0); /* need null terminated argument*/
			err_ret("couldn't execute: %s", buf);
			exit(127);
		}

		/* parent wait the child process to exit*/
		if ((pid = waitpid(pid, &status, 0)) < 0)
			err_sys("waitpid error");
		printf("%% ");
	}
{% endhighlight %}

# 文件空洞

制造空洞文件，文件中的空洞不影响文件的大小。
{% highlight c linenos %}
	int		fd;

	if ((fd = creat("file.hole", FILE_MODE)) < 0)
		err_sys("creat error");

	if (write(fd, buf1, 10) != 10)
		err_sys("buf1 write error");
	/* offset now = 10 */

	if (lseek(fd, 16384, SEEK_SET) == -1)
		err_sys("lseek error");
	/* offset now = 16384 */

	if (write(fd, buf2, 10) != 10)
		err_sys("buf2 write error");
	/* offset now = 16394 */
{% endhighlight %}

# 设置文件状态标志

取文件状态标志，修改后设置回
{% highlight c linenos %}
void
set_fl(int fd, int flags) /* flags are file status flags to turn on */
{
	int		val;

	if ((val = fcntl(fd, F_GETFL, 0)) < 0)
		err_sys("fcntl F_GETFL error");

	val |= flags;		/* turn on flags */
	// val &= ~flag;    /* turn off flags */

	if (fcntl(fd, F_SETFL, val) < 0)
		err_sys("fcntl F_SETFL error");
}
{% endhighlight %}

{% highlight c linenos %}
{% endhighlight %}