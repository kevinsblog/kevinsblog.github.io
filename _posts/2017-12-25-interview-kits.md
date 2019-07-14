---
layout: post
title: 常用编程技巧篇
tags: [C/C++]
bigimg: /img/path.jpg
comments: true
---

* toc
{:toc}

# 判断CPU是大端还是小端

```c
//if CPU is of big endian, return 0, if little endian,
//  return 1
int checkCPU(){
    union w{
        int  a;
        char b;
    } c;
    c.a = 1;
    return c.b == 1;
}
```

小端：低地址存低字节，大端反正
对于16bit数，0x1234，小端存放为
    0x4000: 0x34
    0x4001: 0x12
大端存放为，
    0x4000: 0x12
    0x4001: 0x34

转换大小端，
```c
#define swap32Endian(x)    (((x)&(0x0000ffff)) << 32 | ((x)&(0xffff0000) >> 32)
#define swap64Endian(x)    (((x)&(0x00000000ffffffff)) >> 64 | ((x)&(0xffffffff00000000) >>64)
```

# strcat()

```c
char *do_strcat(char *dst, const char *src){
    if(!src || !*src){
        return dst;
    }
    char *ret = dst;
    while(*dst){dst++;}; //move to the end
    while(*dst++ = *src++); //copy util reach '\0'
    return ret;
}
```

# 两个字符串表述的数相加

```c
void reverseString(char* s, int sSize){
    if(!s || sSize <= 1){
        return;
    }
    
    char *pbeg = s, *pend = s + sSize - 1;
    char tmp;
    while(pbeg < pend){
        tmp = *pbeg;
        *pbeg = *pend;
        *pend = tmp;
        pbeg++;
        pend--;
    }
    
    return;
}

int max(int a, int b){
    return a >= b ? a : b;
}

char * addStrings(char * num1, char * num2){
    int res_len = max(strlen(num1), strlen(num2)) + 2;
    char *res = (char *)calloc(sizeof(char), res_len);
    char *cur = res;
    int carry = 0, n1, n2, n3 = 0;
    reverseString(num1, strlen(num1));
    reverseString(num2, strlen(num2));
    while(*num1 || *num2){
        n1 = *num1 ? *num1 - '0' : 0;
        n2 = *num2 ? *num2 - '0' : 0;
        *cur = (n1 + n2 + carry)%10 + '0';
        carry = (n1 + n2 + carry)/10;
        if(*num1){num1++;}
        if(*num2){num2++;}
        cur++;
    }
    if(carry > 0){
        *cur = carry + '0';
        cur++;
    }
    reverseString(res, strlen(res));
    return res;
}
```

# strcmp()

```c
int do_strcmp(const char *src, const char *dst){
    int ret = 0;
    while(!(ret = *src - *dst) && *dst && *src){
        src++;
        dst++;
    }
    if(ret < 0){
        ret = -1;
    }else if(ret > 0){
        ret = 1;
    }else{
        if(!*dst && !*src){
        }else if(!*src){
            ret = 1; //src is longer, thus greater
        }else{
            ret = -1;
        }
    }
    
    return ret;
}
```

# 验证回文串

```c
bool isPalindrome(char * s){
    const char *beg = s, *end = s;
    while(*end != '\0'){
        end++;
    }
    end--; //move to the last character
    
    while(beg < end){
        //skip no alp or num char
        while(*beg != '\0' && !isalnum(*beg)){beg++;}
        while(end > beg && !isalnum(*end)){end--;}
        if(end <= beg){
            break;
        }
        //check if ok
        if(*beg - *end == 0 || 
          (isalpha(*beg) && isalpha(*end) && abs(*beg - *end) == 'a' - 'A' )){      
        }else{return false;}
        beg++;
        end--;
    }
    
    return true;
}
```

# strrev()

将句子中的单词倒置，但不改变单词的内部结构
```c
char * reverseWords(char * s){
    if(!s){
        return NULL;
    }
    
    char *beg = s, *end = s, *cur = s;
    char tmp;
    while(*cur++){
        if(isspace(*cur) || !*cur){
            end = cur - 1;
            //reverse each word
            while(beg < end){
                tmp = *beg;
                *beg = *end;
                *end = tmp;
                beg++;
                end--;
            }
            beg = end = cur + 1;
        }
    }
    
    beg = s;
    end = cur - 2;
    //reverse the whole sentence
    while(beg < end){
        tmp = *beg;
        *beg = *end;
        *end = tmp;
        beg++;
        end--;        
    }

    return s;
}
```
```
输入 "the sky is blue"
输出 "blue is sky the"
```

# strlen()

移动指针到字符串的末尾，得出字符串长度，不包括‘\0’
```c
int get_strlen(const char *src){
    if(!src){
        return 0;
    }

    const char *tail = src;
    while(*tail++ != '\0'); //指向‘\0’之后一个位置
    return tail - src - 1; //不包括'\0'
}
```

# strstr()

在主串中寻找子串，找到返回位置。
```c
int strStr(char * haystack, char * needle){
    if(!haystack || !needle){
        return 0; //null str
    }
    if(!*haystack && !*needle){
        return 0; //empty str
    }
    const char *ph = haystack, *pn = needle, *start = NULL;
    while(*ph){
        start = ph; //记录主串开始扫描的位置
        pn = needle;
        do{
            if(!*pn){ //子串扫描完成，找到答案
                return start - haystack;
            }
        }while(*pn++ == *ph++); //比较，推进子串和主串
        ph = start+1;
    }
    
    return -1;
```

# strcpy()

复制字符串，以‘\0’结尾
```c
char * do_strcpy(char * dst, const char *src){
    if(!dst || !src){
        return NULL;
    }

    char *dst_cp = dst;
    //先复制，后移动指针，复制完‘\0’后退出循环
    while((*dst++ = *src++) != '\0'){}
    return dst_cp;
}
```

# memcpy()

复制任意内容，指定复制的内存大小
```c
void *do_memcpy(void *to, void *from, size_t n){
    if(!to || !from){
        return NULL;
    }

    char *to_cp = (char*)to;
    char *from_cp = (char*)from; //指针运算前先转成char*
    while(n--){
        *to_cp++ = *from_cp++;
    }
    return to;
}
```

# 运算符优先级

优先级 	运算符 	结合律
1 	后缀运算符：[]    ()    ·    ->    ++    --(类型名称){列表} 	从左到右
2 	一元运算符：++    --    !    ~    +    -    *    &    sizeof_Alignof 	从右到左
3 	类型转换运算符：(类型名称) 	从右到左
4 	乘除法运算符：*    /    % 	从左到右
5 	加减法运算符：+    - 	从左到右
6 	移位运算符：<<    >> 	从左到右
7 	关系运算符：<<=    >>= 	从左到右
8 	相等运算符：==    != 	从左到右
9 	位运算符 AND：& 	从左到右
10 	位运算符 XOR：^ 	从左到右
11 	位运算符 OR：| 	从左到右
12 	逻辑运算符 AND：&& 	从左到右
13 	逻辑运算符 OR：|| 	从左到右
14 	条件运算符：?: 	从右到左
15 	赋值运算符：
     =         +=        -=       *=       /=      %=       &=       ^=      |=   
   <<=      >>= 	从右到左
16 	逗号运算符：， 	从左到右

# const的作用

* 定义常量，替代宏
* const &，替代传值
* const void *，返回不能修改值得指针
* 修饰成员函数，避免其修改数据

# static的作用

* 声明一个块作用域内的持续性变量，静态存储方式，改变变量生存期
* 声明只在文件内可见的变量或函数，限制作用域
* 声明一个类实例共享的成员变量或函数，::

# sizeof结构体



# 指针数组与数组指针

指针数组是是一个数组，数组中存放的都是同一个类型的指针。
```
int *a[10];
```
数组a中存放了10个int*型的变量。

数组指针是一个指针，指向一个数组。
代码
```c
    //char *str[] = {"Welcom", "to", "Fortemedia", "Nanjing"};
    //A, B, C, D
    const char *str[] = {"Welcom", "to", "Fortemedia", "Nanjing"};
    const char **p = str + 1; //p : address of "to", B
    str[0] = (*p++)+2; //NULL, p: C, 
    str[1] = *(p+1); //"Nanjing", D
    str[2] = p[1] + 3; //"jing", D[3]
    str[3] = p[0] + (str[2] - str[1]); // "g", p[0] + 3

    printf("str[0]: %s, str[1]: %s, str[2]: %s, str[3]: %s \n",
        str[0], str[1], str[2], str[3]);
```
运行结果，
```
str[0]: , str[1]: Nanjing, str[2]: jing, str[3]: g
```

# 函数指针与指针函数

指针函数是一个函数，返回类型为某一类型的指针。

每一个函数，都有一个入口地址，函数指针是指向函数的这一入口地址的。

```c
    float score[] = {10, 20, 30 ,40};
    int (*p)(int, int);
    float *q = ret_ele_at(score+1, 1); //30.0 //指针函数
    p = ret_max;
    int a = (*p)(1, 2); //2 //函数指针
    printf("a: %d, *q: %f\n", a, *q);
```
运行结果
```
a: 2, *q: 30.000000
```
综上，
```
    int* a[10]; //指针数组，类型为指针的数组
    int *a = {20, 30, 40}; //数组指针，指向数组的指针
    void (*fn)(int *, int); //函数指针，指向函数的指针
    int (*fnA[10])(int *, int); //函数指针数组，指向函数的指针数组
    int* fn(int); //返回指针的函数
    const int *p; //指向常量的指针，指针指向的值不能修改
    int* const p; //常量指针，指针的指向不能修改

    typedef int (*pfun)(int, int);
    ...
    pfun p = fun;
```

# 读写文件

读写文件的过程
{% highlight c++ linenos %}
#include <fstream>
void handlingFiles(){
    fstream file;
    char text[256];
    file.open("example.txt", ios::out | ios::in);
    cout << "write text to file" << endl;
    cin.getline(text, sizeof(text));
    file << text << endl; //write
    file >> text; //read
    cout << text << endl;
    file.close();
}
{% endhighlight %}


# 分解字符串

C/C++中缺乏按格式分解字符串的方法，我们常常需要自己实现它。这里列举一种我知道的最简便的方法。

{% highlight c++ linenos %}
#include <sstream>
vector<string> tokenize(const string &s, const char &delimiter){
    vector<string> tokens;
    stringstream ss(s); //convert string to stream
    string tmp;
    //get lines from stream
    while(getline(ss, tmp, delimiter)){
        if(!tmp.empty())
            tokens.push_back(tmp); //save tokens
    }
    return move(tokens);
}
{% endhighlight %}

写一个驱动程序测试一下，
{% highlight c++ linenos %}
void tokenizeDemo(){
    string s = "hello world \n hello paopao \n bye bye \n";
    auto lines = tokenize(s, '\n'); //tokenize by line
    for(auto & line : lines){ 
        auto items = tokenize(line, ' '); //by space
        for(auto & item : items){
            cout << "|"<< item <<"|"<< endl;
        }
    }
}
{% endhighlight %}
看一下测试结果,
```
|hello|
|world|
|hello|
|paopao|
|bye|
|bye|
```

# Memcpy的实现

不调用C/C++库函数，实现memcpy。

{% highlight c++ linenos %}
void * memcpy(void *dst, void *src, size_t cnt){
    assert(dst != nullptr && src != nullptr);
    unsigned char *pdst = (unsigned char *)dst;
    const unsigned char *psrc = (const unsigned char *)src;

    //make sure no overlap between two memory region
    assert(!(psrc <= pdst && pdst < psrc + cnt) && 
            !(pdst <= psrc && psrc < pdst + cnt));

    while(cnt--){
        *pdst++ = *psrc++;
    }

    return dst;
}

void memcpyTest(){
    //copy str from src to dst and print out
    char src[256] = "hello my friend", dst[256];
    cout << (char *)memcpy(dst, src, strlen(src)+1)<<endl;
}
{% endhighlight %}
测试一下
```
hello my friend
```

# Memove的实现

memove实现的是剪切粘贴的功能，允许内存存在重叠区，可能覆盖原数据。

{% highlight c++ linenos %}
void *memmove(void *dst, void *src, size_t cnt){
    void *ret = dst;
    unsigned char *pdst = (unsigned char *)dst;
    const unsigned char *psrc = (const unsigned char *)src;

    if(pdst < psrc || pdst >= (psrc + cnt)){ //if overlap not exists
        //copy from start to end
        while(cnt--){
            *pdst++ = *psrc++;
        }
    }else{ //if overlap exists
        pdst += cnt - 1;
        psrc += cnt - 1;
        //copy from end to start
        while(cnt--){
            *pdst-- = *psrc--;
        }
    }

    return ret;
}

void memmoveTest(){
    char buf[256] = "hello my friend";
    char *dst = (char *)memmove(buf + 10, buf, strlen(buf)+1);
    printf("%p->%p: %s\n", buf, dst, dst);
}
{% endhighlight %}
测试一下
```
0x7ffee7e1e6a0->0x7ffee7e1e6aa: hello my friend
```

# 字符串格式控制符一览

{% highlight c++ linenos %}
printf("%d, %o, %x\n", 10, 10, 10); //print int in 十进制，八进制和十六进制, new line
printf("%ld\n", INT_MAX + 1);
printf("%10d\n", 236); //指定输出字段的宽度，左端用空格补缺
printf("%u\n", -2);
printf("%3.2f\n", -34.5678);
printf("%s\n", "ok");
//输出小写，大写，带0x的十六进制数
printf("%x, %X, %#x, %#X\n", 47, 47, 47, 47);
printf("\\n\n"); //打印转义字符
printf("\\0\n");
{% endhighlight %}

输出结果

{% highlight c++ linenos %}
10, 12, a
2147483648
       236
4294967294
-34.57
ok
2f, 2F, 0x2f, 0X2F
\n
23600
{% endhighlight %}

```
printf("%zd\n", -10); //%zd需要一个正数，描述size_t
```
```
warning: format specifies type 'ssize_t' (aka 'long') but the
      argument has type 'int' [-Wformat]
    printf("%zd\n", -10);
```

# ASCII码表

空字符\0的ASCII值为0。
数字0从48开始。
字母A从65开始。
字母a从97开始。

ASCII控制字符
二进制 	十进制 	十六进制 	缩写 	可以显示的表示法 	名称/意义
0000 0000 	0 	00 	NUL 	␀ 	空字符（Null）*
0000 0001 	1 	01 	SOH 	␁ 	标题开始
0000 0010 	2 	02 	STX 	␂ 	本文开始
0000 0011 	3 	03 	ETX 	␃ 	本文结束
0000 0100 	4 	04 	EOT 	␄ 	传输结束
0000 0101 	5 	05 	ENQ 	␅ 	请求
0000 0110 	6 	06 	ACK 	␆ 	确认回应
0000 0111 	7 	07 	BEL 	␇ 	响铃
0000 1000 	8 	08 	BS 	␈ 	退格
0000 1001 	9 	09 	HT 	␉ 	水平定位符号
0000 1010 	10 	0A 	LF 	␊ 	换行键 *
0000 1011 	11 	0B 	VT 	␋ 	垂直定位符号
0000 1100 	12 	0C 	FF 	␌ 	换页键
0000 1101 	13 	0D 	CR 	␍ 	归位键 *
0000 1110 	14 	0E 	SO 	␎ 	取消变换（Shift out）
0000 1111 	15 	0F 	SI 	␏ 	启用变换（Shift in）
0001 0000 	16 	10 	DLE 	␐ 	跳出数据通讯
0001 0001 	17 	11 	DC1 	␑ 	设备控制一（XON 启用软件速度控制）
0001 0010 	18 	12 	DC2 	␒ 	设备控制二
0001 0011 	19 	13 	DC3 	␓ 	设备控制三（XOFF 停用软件速度控制）
0001 0100 	20 	14 	DC4 	␔ 	设备控制四
0001 0101 	21 	15 	NAK 	␕ 	确认失败回应
0001 0110 	22 	16 	SYN 	␖ 	同步用暂停
0001 0111 	23 	17 	ETB 	␗ 	区块传输结束
0001 1000 	24 	18 	CAN 	␘ 	取消
0001 1001 	25 	19 	EM 	␙ 	连接介质中断
0001 1010 	26 	1A 	SUB 	␚ 	替换
0001 1011 	27 	1B 	ESC 	␛ 	跳出
0001 1100 	28 	1C 	FS 	␜ 	文件分割符
0001 1101 	29 	1D 	GS 	␝ 	组群分隔符
0001 1110 	30 	1E 	RS 	␞ 	记录分隔符
0001 1111 	31 	1F 	US 	␟ 	单元分隔符
0111 1111 	127 	7F 	DEL 	␡ 	删除

ASCII可显示字符
二进制 	十进制 	十六进制 	图形
0010 0000 	32 	20 	（空格）(␠) *
0010 0001 	33 	21 	!
0010 0010 	34 	22 	"
0010 0011 	35 	23 	#
0010 0100 	36 	24 	$
0010 0101 	37 	25 	 %
0010 0110 	38 	26 	&
0010 0111 	39 	27 	'
0010 1000 	40 	28 	(
0010 1001 	41 	29 	)
0010 1010 	42 	2A 	*
0010 1011 	43 	2B 	+
0010 1100 	44 	2C 	,
0010 1101 	45 	2D 	-
0010 1110 	46 	2E 	.
0010 1111 	47 	2F 	/
0011 0000 	48 	30 	0 *
0011 0001 	49 	31 	1
0011 0010 	50 	32 	2
0011 0011 	51 	33 	3
0011 0100 	52 	34 	4
0011 0101 	53 	35 	5
0011 0110 	54 	36 	6
0011 0111 	55 	37 	7
0011 1000 	56 	38 	8
0011 1001 	57 	39 	9
0011 1010 	58 	3A 	:
0011 1011 	59 	3B 	;
0011 1100 	60 	3C 	<
0011 1101 	61 	3D 	=
0011 1110 	62 	3E 	>
0011 1111 	63 	3F 	?
	  	
二进制 	十进制 	十六进制 	图形
0100 0000 	64 	40 	@
0100 0001 	65 	41 	A *
0100 0010 	66 	42 	B
0100 0011 	67 	43 	C
0100 0100 	68 	44 	D
0100 0101 	69 	45 	E
0100 0110 	70 	46 	F
0100 0111 	71 	47 	G
0100 1000 	72 	48 	H
0100 1001 	73 	49 	I
0100 1010 	74 	4A 	J
0100 1011 	75 	4B 	K
0100 1100 	76 	4C 	L
0100 1101 	77 	4D 	M
0100 1110 	78 	4E 	N
0100 1111 	79 	4F 	O
0101 0000 	80 	50 	P
0101 0001 	81 	51 	Q
0101 0010 	82 	52 	R
0101 0011 	83 	53 	S
0101 0100 	84 	54 	T
0101 0101 	85 	55 	U
0101 0110 	86 	56 	V
0101 0111 	87 	57 	W
0101 1000 	88 	58 	X
0101 1001 	89 	59 	Y
0101 1010 	90 	5A 	Z
0101 1011 	91 	5B 	[
0101 1100 	92 	5C 	\
0101 1101 	93 	5D 	]
0101 1110 	94 	5E 	^
0101 1111 	95 	5F 	_
	  	
二进制 	十进制 	十六进制 	图形
0110 0000 	96 	60 	`
0110 0001 	97 	61 	a *
0110 0010 	98 	62 	b
0110 0011 	99 	63 	c
0110 0100 	100 	64 	d
0110 0101 	101 	65 	e
0110 0110 	102 	66 	f
0110 0111 	103 	67 	g
0110 1000 	104 	68 	h
0110 1001 	105 	69 	i
0110 1010 	106 	6A 	j
0110 1011 	107 	6B 	k
0110 1100 	108 	6C 	l
0110 1101 	109 	6D 	m
0110 1110 	110 	6E 	n
0110 1111 	111 	6F 	o
0111 0000 	112 	70 	p
0111 0001 	113 	71 	q
0111 0010 	114 	72 	r
0111 0011 	115 	73 	s
0111 0100 	116 	74 	t
0111 0101 	117 	75 	u
0111 0110 	118 	76 	v
0111 0111 	119 	77 	w
0111 1000 	120 	78 	x
0111 1001 	121 	79 	y
0111 1010 	122 	7A 	z
0111 1011 	123 	7B 	{
0111 1100 	124 	7C 	|
0111 1101 	125 	7D 	}
0111 1110 	126 	7E 	~


{% highlight c++ linenos %}

{% endhighlight %}