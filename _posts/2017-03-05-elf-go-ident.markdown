---
layout: post
style: text
title: A Deep dive in go binaries
---

Once upon a time, I used to work on file formats, debuggers etc, and sometimes used to blog about them as well. 
While many of them were permanently lost in the graveyard that is internet, [some survived](http://qasim.zaidi.me/2010/02/what-is-in-heap.html) 

Off late, I got interested into thinking about ELF and go again. Once a priest, always a priest, huh?

Well, the itch started because we use this tool called [gops](https://github.com/google/gops) from google. 
Apart from other things, it can give you a stack trace of all your go-routines, which we find quite handy in debugging issues, esp the infamous [too many open files](https://github.com/golang/go/blob/045ad5bab812657a85707e480c29de9144881be1/src/net/http/server.go#L2665)

So while all is well with this tool, currently it uses the presence of runtime.buildVersion in the symbol table, to identify if a program is golang.

```
nm <go-binary> | grep runtime.buildVersion
0000000000a94d20 d runtime.buildVersion
```

The lowercase *d* after the address suggests that this is a local data section symbol. If you are curious enough, you can actually find out which go
version was used to build the process, with a little help from objdump. (gobjdump for MacOS, installable via brew).

First, we check out the contents of the data at this address indicated in the symbol table. 

```
gobjdump -s --start-addr 0xa94d20 --stop-addr 0xa94d24 <go-binary>
<go-binary>:     file format mach-o-x86-64

Contents of section .data:
 a94d20 93507200                             .Pr.      
```

As expected, its the data segment, and the data stored is 93507200. Now since the ELF format storage is Least Significant Byte First (LSB),
93507200 has to be actually read as 0x00725093. Let's fire up objdump again, and see what is stored.

```
gobjdump -s --start-addr 0x725093 --stop-addr 0x72509c <go-binary> 

<go-binary>:     file format mach-o-x86-64

Contents of section __TEXT.__rodata:
 725093 67 6f312e37 2e34676f                 go1.7.4go      
```

Which tells us that this binary was compiled using go 1.7.4 compiler

Now this works pretty well, except for the fact that we use a standard debian build script which strips the binaries. (See man strip) 
The process of stripping removes the symbol table, since  you don't need the symtab except for debugging. And hence, the above scheme breaks, and gops can't identify the binary correctly as a go binary.

While we can comment out the strip during build, the size difference between a stripped binary and one that isn't is quite significant, so rather than fix all of our debian/rules script, I am thinking about 
what other alternatives might exist.  

Some possible approaches

===strings===

```
strings -n 8 <go-binary> | grep runtime.interface
*runtime.interfacetype
runtime.interfacetype
runtime.interfacetype
*runtime.interfacetype
runtime.interfacetype
runtime.interfacetype
*runtime.interfacetype
```
=== Readelf ===

readelf is a bit more powerful than objdump. We can use it first to read the ELF header

```
readelf -h /usr/bin/<go-binary>
ELF Header:
  Magic:   7f 45 4c 46 02 01 01 00 00 00 00 00 00 00 00 00 
  Class:                             ELF64
  Data:                              2's complement, little endian
  Version:                           1 (current)
  OS/ABI:                            UNIX - System V
  ABI Version:                       0
  Type:                              EXEC (Executable file)
  Machine:                           Advanced Micro Devices X86-64
  Version:                           0x1
  Entry point address:               0x45f800
  Start of program headers:          64 (bytes into file)
  Start of section headers:          6517168 (bytes into file)
  Flags:                             0x0
  Size of this header:               64 (bytes)
  Size of program headers:           56 (bytes)
  Number of program headers:         10
  Size of section headers:           64 (bytes)
  Number of section headers:         26
  Section header string table index: 25
```

Now let's see what is there in the *Section header string table*

```
readelf -p 25 /usr/bin/<go-binary>

String dump of section '.shstrtab':
  [     1]  .shstrtab
  [     b]  .text
  [    11]  .rodata
  [    19]  .typelink
  [    23]  .itablink
  [    2d]  .gosymtab
  [    37]  .gopclntab
  [    42]  .dynsym
  [    4a]  .rela
  [    50]  .rela.plt
  [    5a]  .gnu.version
  [    67]  .gnu.version_r
  [    76]  .hash
  [    7c]  .dynstr
  [    84]  .got.plt
  [    8d]  .dynamic
  [    96]  .got
  [    9b]  .noptrdata
  [    a6]  .data
  [    ac]  .bss
  [    b1]  .noptrbss
  [    bb]  .tbss
  [    c1]  .interp
  [    c9]  .note.go.buildid
  ```
As you can see, a couple of things look very go specific, namely .gosymtab, .note.go.buildid

This is a developing story, and I will be updating the post as I play more with the ELF files. 

Note that none of this matters, since gops still works, we have  other work-arounds (e.g, don't strip the binary), but then, random explorations is what this blog is all about. Hope you find it interesting.
