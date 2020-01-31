Exercise #1: Spying on a the du program [16 pts]

[q1 (2pts)] What is the full absolute path to the standard C library file on your system? (since du is written in C, it should at some point open the standard C librarys .so file, which should have some easy-to-recognize name). Cut-and-paste into your report the line of straces output that shows the path to the standard C library.
open("/lib/x86_64-linux-gnu/libc.so.6", O_RDONLY|O_CLOEXEC) = 3

[q2 (3pts)] How many times does du try to open a file but fails? Explain how you counted this number (or just show your command-line if you used command-line tool to do this).
strace -c du /, tries to open a file 28 times and fails 24.
1226557 /
% time     seconds  usecs/call     calls    errors syscall
------ ----------- ----------- --------- --------- ----------------
 34.94    0.145748           6     23215           write
 31.38    0.130899           1    162255         4 newfstatat
 11.84    0.049391           0    135505           fcntl
  6.77    0.028259           1     38566           getdents
  5.83    0.024306           1     20339       784 openat
  5.15    0.021500           1     19560           fstat
  4.08    0.017039           0     38840           close
  0.00    0.000000           0         3           read
  0.00    0.000000           0        28        24 open
  0.00    0.000000           0         9           mmap
  0.00    0.000000           0         4           mprotect
  0.00    0.000000           0         2           munmap
  0.00    0.000000           0        47           brk
  0.00    0.000000           0         3         3 access
  0.00    0.000000           0         1           execve
  0.00    0.000000           0         1           fstatfs
  0.00    0.000000           0         1           arch_prctl
------ ----------- ----------- --------- --------- ----------------
100.00    0.417142                438379       815 total

[q3 (3pts)] How many different system calls does du place? Explain how you made this determination? (this strace should be a number between 0 and 30)
17. By running the strace -c du /, the output is a summary of system calls, counting them there were a total of 17.

[q4 (2pts)] Cut-and-paste the output of time (which will vary slightly from one invocation to the next, just pick one)
real    0m1.147s
user    0m0.184s
sys     0m0.460s

[q5 (3pts)] Based on the output of time, what percentage of the execution time of the du command is spent doing I/O? Show your work.
I/O = real - user - sys
    = 1.147s - 0.184s - 0.460s
    = 0.503s

[q6 (3pts)] Based on the output of time and the strace output in previous question, how much CPU time, in microseconds, is spent on average to execute the instructions of a system call (counting only those calls that succeed)? Show your work.
time in usec = (total seconds in microseconds/(calls-errors)
             = 417142/(438379-815)
             = 0.953328 usecs/call

Exercise #2: Is wget getting what its asking for? [24 pts]

[q1 (3pts)] How many system calls are placed by wget before it receives the first bytes from the file content? Explain how you made that determination.
There are 296 system calls placed before it receives the first bytes from the file. I determined this by tracing all the system calls made, then counting the number of calls before accessing the first lines of text, "The Project Gutenberg..."
strace -e trace=all wget http://norvig.com/big.txt /
execve("/usr/bin/wget", ["wget", "http://norvig.com/big.txt", "/"], [/* 20 vars */]) = 0
brk(NULL)                               = 0x563cca957000
access("/etc/ld.so.nohwcap", F_OK)      = -1 ENOENT (No such file or directory)
access("/etc/ld.so.preload", R_OK)      = -1 ENOENT (No such file or directory)
open("/etc/ld.so.cache", O_RDONLY|O_CLOEXEC) = 3
fstat(3, {st_mode=S_IFREG|0644, st_size=21118, ...}) = 0
mmap(NULL, 21118, PROT_READ, MAP_PRIVATE, 3, 0) = 0x7fc2d4e17000
close(3)                                = 0
access("/etc/ld.so.nohwcap", F_OK)      = -1 ENOENT (No such file or directory)
open("/lib/x86_64-linux-gnu/libpcre.so.3", O_RDONLY|O_CLOEXEC) = 3
read(3, "\177ELF\2\1\1\0\0\0\0\0\0\0\0\0\3\0>\0\1\0\0\0000\25\0\0\0\0\0\0"..., 832) = 832
fstat(3, {st_mode=S_IFREG|0644, st_size=456632, ...}) = 0
mmap(NULL, 4096, PROT_READ|PROT_WRITE, MAP_PRIVATE|MAP_ANONYMOUS, -1, 0) = 0x7fc2d4e16000
mmap(NULL, 2552072, PROT_READ|PROT_EXEC, MAP_PRIVATE|MAP_DENYWRITE, 3, 0) = 0x7fc2d4988000
mprotect(0x7fc2d49f6000, 2097152, PROT_NONE) = 0
mmap(0x7fc2d4bf6000, 8192, PROT_READ|PROT_WRITE, MAP_PRIVATE|MAP_FIXED|MAP_DENYWRITE, 3, 0x6e000) = 0x7fc2d4bf6000
close(3)                                = 0
access("/etc/ld.so.nohwcap", F_OK)      = -1 ENOENT (No such file or directory)
open("/lib/x86_64-linux-gnu/libuuid.so.1", O_RDONLY|O_CLOEXEC) = 3
read(3, "\177ELF\2\1\1\0\0\0\0\0\0\0\0\0\3\0>\0\1\0\0\0\20\25\0\0\0\0\0\0"..., 832) = 832
fstat(3, {st_mode=S_IFREG|0644, st_size=18976, ...}) = 0
mmap(NULL, 2113904, PROT_READ|PROT_EXEC, MAP_PRIVATE|MAP_DENYWRITE, 3, 0) = 0x7fc2d4783000
mprotect(0x7fc2d4787000, 2093056, PROT_NONE) = 0
mmap(0x7fc2d4986000, 8192, PROT_READ|PROT_WRITE, MAP_PRIVATE|MAP_FIXED|MAP_DENYWRITE, 3, 0x3000) = 0x7fc2d4986000
close(3)                                = 0
access("/etc/ld.so.nohwcap", F_OK)      = -1 ENOENT (No such file or directory)
open("/lib/x86_64-linux-gnu/libssl.so.1.0.0", O_RDONLY|O_CLOEXEC) = 3
read(3, "\177ELF\2\1\1\0\0\0\0\0\0\0\0\0\3\0>\0\1\0\0\0\320^\1\0\0\0\0\0"..., 832) = 832
fstat(3, {st_mode=S_IFREG|0644, st_size=428384, ...}) = 0
mmap(NULL, 2523600, PROT_READ|PROT_EXEC, MAP_PRIVATE|MAP_DENYWRITE, 3, 0) = 0x7fc2d451a000
mprotect(0x7fc2d4578000, 2097152, PROT_NONE) = 0
mmap(0x7fc2d4778000, 45056, PROT_READ|PROT_WRITE, MAP_PRIVATE|MAP_FIXED|MAP_DENYWRITE, 3, 0x5e000) = 0x7fc2d4778000
close(3)                                = 0
access("/etc/ld.so.nohwcap", F_OK)      = -1 ENOENT (No such file or directory)
open("/lib/x86_64-linux-gnu/libcrypto.so.1.0.0", O_RDONLY|O_CLOEXEC) = 3
read(3, "\177ELF\2\1\1\0\0\0\0\0\0\0\0\0\3\0>\0\1\0\0\0\0\36\6\0\0\0\0\0"..., 832) = 832
fstat(3, {st_mode=S_IFREG|0644, st_size=2365952, ...}) = 0
mmap(NULL, 4096, PROT_READ|PROT_WRITE, MAP_PRIVATE|MAP_ANONYMOUS, -1, 0) = 0x7fc2d4e15000
mmap(NULL, 4475776, PROT_READ|PROT_EXEC, MAP_PRIVATE|MAP_DENYWRITE, 3, 0) = 0x7fc2d40d5000
mprotect(0x7fc2d42f0000, 2093056, PROT_NONE) = 0
mmap(0x7fc2d44ef000, 163840, PROT_READ|PROT_WRITE, MAP_PRIVATE|MAP_FIXED|MAP_DENYWRITE, 3, 0x21a000) = 0x7fc2d44ef000
mmap(0x7fc2d4517000, 11136, PROT_READ|PROT_WRITE, MAP_PRIVATE|MAP_FIXED|MAP_ANONYMOUS, -1, 0) = 0x7fc2d4517000
close(3)                                = 0
access("/etc/ld.so.nohwcap", F_OK)      = -1 ENOENT (No such file or directory)
open("/lib/x86_64-linux-gnu/libz.so.1", O_RDONLY|O_CLOEXEC) = 3
read(3, "\177ELF\2\1\1\0\0\0\0\0\0\0\0\0\3\0>\0\1\0\0\0\260\35\0\0\0\0\0\0"..., 832) = 832
fstat(3, {st_mode=S_IFREG|0644, st_size=104864, ...}) = 0
mmap(NULL, 2199848, PROT_READ|PROT_EXEC, MAP_PRIVATE|MAP_DENYWRITE, 3, 0) = 0x7fc2d3ebb000
mprotect(0x7fc2d3ed4000, 2093056, PROT_NONE) = 0
mmap(0x7fc2d40d3000, 8192, PROT_READ|PROT_WRITE, MAP_PRIVATE|MAP_FIXED|MAP_DENYWRITE, 3, 0x18000) = 0x7fc2d40d3000
close(3)                                = 0
access("/etc/ld.so.nohwcap", F_OK)      = -1 ENOENT (No such file or directory)
open("/usr/lib/x86_64-linux-gnu/libidn.so.11", O_RDONLY|O_CLOEXEC) = 3
read(3, "\177ELF\2\1\1\0\0\0\0\0\0\0\0\0\3\0>\0\1\0\0\0p+\0\0\0\0\0\0"..., 832) = 832
fstat(3, {st_mode=S_IFREG|0644, st_size=207208, ...}) = 0
mmap(NULL, 2302192, PROT_READ|PROT_EXEC, MAP_PRIVATE|MAP_DENYWRITE, 3, 0) = 0x7fc2d3c88000
mprotect(0x7fc2d3cb9000, 2097152, PROT_NONE) = 0
mmap(0x7fc2d3eb9000, 8192, PROT_READ|PROT_WRITE, MAP_PRIVATE|MAP_FIXED|MAP_DENYWRITE, 3, 0x31000) = 0x7fc2d3eb9000
close(3)                                = 0
access("/etc/ld.so.nohwcap", F_OK)      = -1 ENOENT (No such file or directory)
open("/lib/x86_64-linux-gnu/libc.so.6", O_RDONLY|O_CLOEXEC) = 3
read(3, "\177ELF\2\1\1\3\0\0\0\0\0\0\0\0\3\0>\0\1\0\0\0P\t\2\0\0\0\0\0"..., 832) = 832
fstat(3, {st_mode=S_IFREG|0755, st_size=1868984, ...}) = 0
mmap(NULL, 4096, PROT_READ|PROT_WRITE, MAP_PRIVATE|MAP_ANONYMOUS, -1, 0) = 0x7fc2d4e14000
mmap(NULL, 3971488, PROT_READ|PROT_EXEC, MAP_PRIVATE|MAP_DENYWRITE, 3, 0) = 0x7fc2d38be000
mprotect(0x7fc2d3a7e000, 2097152, PROT_NONE) = 0
mmap(0x7fc2d3c7e000, 24576, PROT_READ|PROT_WRITE, MAP_PRIVATE|MAP_FIXED|MAP_DENYWRITE, 3, 0x1c0000) = 0x7fc2d3c7e000
mmap(0x7fc2d3c84000, 14752, PROT_READ|PROT_WRITE, MAP_PRIVATE|MAP_FIXED|MAP_ANONYMOUS, -1, 0) = 0x7fc2d3c84000
close(3)                                = 0
access("/etc/ld.so.nohwcap", F_OK)      = -1 ENOENT (No such file or directory)
open("/lib/x86_64-linux-gnu/libpthread.so.0", O_RDONLY|O_CLOEXEC) = 3
read(3, "\177ELF\2\1\1\0\0\0\0\0\0\0\0\0\3\0>\0\1\0\0\0\260`\0\0\0\0\0\0"..., 832) = 832
fstat(3, {st_mode=S_IFREG|0755, st_size=138696, ...}) = 0
mmap(NULL, 2212904, PROT_READ|PROT_EXEC, MAP_PRIVATE|MAP_DENYWRITE, 3, 0) = 0x7fc2d36a1000
mprotect(0x7fc2d36b9000, 2093056, PROT_NONE) = 0
mmap(0x7fc2d38b8000, 8192, PROT_READ|PROT_WRITE, MAP_PRIVATE|MAP_FIXED|MAP_DENYWRITE, 3, 0x17000) = 0x7fc2d38b8000
mmap(0x7fc2d38ba000, 13352, PROT_READ|PROT_WRITE, MAP_PRIVATE|MAP_FIXED|MAP_ANONYMOUS, -1, 0) = 0x7fc2d38ba000
close(3)                                = 0
access("/etc/ld.so.nohwcap", F_OK)      = -1 ENOENT (No such file or directory)
open("/lib/x86_64-linux-gnu/libdl.so.2", O_RDONLY|O_CLOEXEC) = 3
read(3, "\177ELF\2\1\1\0\0\0\0\0\0\0\0\0\3\0>\0\1\0\0\0\240\r\0\0\0\0\0\0"..., 832) = 832
fstat(3, {st_mode=S_IFREG|0644, st_size=14608, ...}) = 0
mmap(NULL, 2109680, PROT_READ|PROT_EXEC, MAP_PRIVATE|MAP_DENYWRITE, 3, 0) = 0x7fc2d349d000
mprotect(0x7fc2d34a0000, 2093056, PROT_NONE) = 0
mmap(0x7fc2d369f000, 8192, PROT_READ|PROT_WRITE, MAP_PRIVATE|MAP_FIXED|MAP_DENYWRITE, 3, 0x2000) = 0x7fc2d369f000
close(3)                                = 0
mmap(NULL, 4096, PROT_READ|PROT_WRITE, MAP_PRIVATE|MAP_ANONYMOUS, -1, 0) = 0x7fc2d4e13000
mmap(NULL, 4096, PROT_READ|PROT_WRITE, MAP_PRIVATE|MAP_ANONYMOUS, -1, 0) = 0x7fc2d4e12000
mmap(NULL, 8192, PROT_READ|PROT_WRITE, MAP_PRIVATE|MAP_ANONYMOUS, -1, 0) = 0x7fc2d4e10000
arch_prctl(ARCH_SET_FS, 0x7fc2d4e10780) = 0
mprotect(0x7fc2d3c7e000, 16384, PROT_READ) = 0
mprotect(0x7fc2d369f000, 4096, PROT_READ) = 0
mprotect(0x7fc2d38b8000, 4096, PROT_READ) = 0
mprotect(0x7fc2d3eb9000, 4096, PROT_READ) = 0
mprotect(0x7fc2d40d3000, 4096, PROT_READ) = 0
mprotect(0x7fc2d44ef000, 114688, PROT_READ) = 0
mprotect(0x7fc2d4778000, 16384, PROT_READ) = 0
mprotect(0x7fc2d4986000, 4096, PROT_READ) = 0
mprotect(0x7fc2d4bf6000, 4096, PROT_READ) = 0
mprotect(0x563cca5e3000, 16384, PROT_READ) = 0
mprotect(0x7fc2d4e1d000, 4096, PROT_READ) = 0
munmap(0x7fc2d4e17000, 21118)           = 0
set_tid_address(0x7fc2d4e10a50)         = 3686
set_robust_list(0x7fc2d4e10a60, 24)     = 0
rt_sigaction(SIGRTMIN, {0x7fc2d36a6b50, [], SA_RESTORER|SA_SIGINFO, 0x7fc2d36b2390}, NULL, 8) = 0
rt_sigaction(SIGRT_1, {0x7fc2d36a6be0, [], SA_RESTORER|SA_RESTART|SA_SIGINFO, 0x7fc2d36b2390}, NULL, 8) = 0
rt_sigprocmask(SIG_UNBLOCK, [RTMIN RT_1], NULL, 8) = 0
getrlimit(RLIMIT_STACK, {rlim_cur=8192*1024, rlim_max=RLIM64_INFINITY}) = 0
brk(NULL)                               = 0x563cca957000
brk(0x563cca978000)                     = 0x563cca978000
clock_getres(CLOCK_MONOTONIC, {0, 1})   = 0
clock_getres(CLOCK_MONOTONIC, {0, 1})   = 0
open("/usr/lib/locale/locale-archive", O_RDONLY|O_CLOEXEC) = 3
fstat(3, {st_mode=S_IFREG|0644, st_size=1668976, ...}) = 0
mmap(NULL, 1668976, PROT_READ, MAP_PRIVATE, 3, 0) = 0x7fc2d4c78000
close(3)                                = 0
stat("/etc/wgetrc", {st_mode=S_IFREG|0644, st_size=4942, ...}) = 0
open("/etc/wgetrc", O_RDONLY)           = 3
fstat(3, {st_mode=S_IFREG|0644, st_size=4942, ...}) = 0
read(3, "###\n### Sample Wget initializati"..., 4096) = 4096
read(3, "ruct = off\n\n# You can turn on re"..., 4096) = 846
read(3, "", 4096)                       = 0
close(3)                                = 0
stat("/home/vagrant/.wgetrc", 0x7ffcc9408d80) = -1 ENOENT (No such file or directory)
ioctl(2, TCGETS, {B9600 opost isig icanon echo ...}) = 0
ioctl(2, TCGETS, {B9600 opost isig icanon echo ...}) = 0
rt_sigaction(SIGHUP, {SIG_IGN, [HUP], SA_RESTORER|SA_RESTART, 0x7fc2d38f34b0}, {SIG_DFL, [], 0}, 8) = 0
rt_sigaction(SIGHUP, {0x563cca3a2880, [HUP], SA_RESTORER|SA_RESTART, 0x7fc2d38f34b0}, {SIG_IGN, [HUP], SA_RESTORER|SA_RESTART, 0x7fc2d38f34b0}, 8) = 0
rt_sigaction(SIGUSR1, {0x563cca3a2880, [USR1], SA_RESTORER|SA_RESTART, 0x7fc2d38f34b0}, {SIG_DFL, [], 0}, 8) = 0
rt_sigaction(SIGPIPE, {SIG_IGN, [PIPE], SA_RESTORER|SA_RESTART, 0x7fc2d38f34b0}, {SIG_DFL, [], 0}, 8) = 0
rt_sigaction(SIGWINCH, {0x563cca3a4320, [WINCH], SA_RESTORER|SA_RESTART, 0x7fc2d38f34b0}, {SIG_DFL, [], 0}, 8) = 0
stat("/home/vagrant/.wget-hsts", 0x7ffcc9408cd0) = -1 ENOENT (No such file or directory)
statfs(".", {f_type="EXT2_SUPER_MAGIC", f_bsize=4096, f_blocks=2524617, f_bfree=2199330, f_bavail=2195234, f_files=1280000, f_ffree=1191039, f_fsid={-1288372858, 2084079672}, f_namelen=255, f_frsize=4096, f_flags=4128}) = 0
stat("big.txt", {st_mode=S_IFREG|0664, st_size=6488666, ...}) = 0
stat("big.txt.1", {st_mode=S_IFREG|0664, st_size=6488666, ...}) = 0
stat("big.txt.2", {st_mode=S_IFREG|0664, st_size=6488666, ...}) = 0
stat("big.txt.3", {st_mode=S_IFREG|0664, st_size=6488666, ...}) = 0
stat("big.txt.4", {st_mode=S_IFREG|0664, st_size=6488666, ...}) = 0
stat("big.txt.5", {st_mode=S_IFREG|0664, st_size=6488666, ...}) = 0
stat("big.txt.6", {st_mode=S_IFREG|0664, st_size=6488666, ...}) = 0
stat("big.txt.7", {st_mode=S_IFREG|0664, st_size=6488666, ...}) = 0
stat("big.txt.8", {st_mode=S_IFREG|0664, st_size=6488666, ...}) = 0
stat("big.txt.9", {st_mode=S_IFREG|0664, st_size=6488666, ...}) = 0
stat("big.txt.10", {st_mode=S_IFREG|0664, st_size=6488666, ...}) = 0
stat("big.txt.11", 0x7ffcc9408820)      = -1 ENOENT (No such file or directory)
stat("big.txt.11", 0x7ffcc9408990)      = -1 ENOENT (No such file or directory)
open("/etc/localtime", O_RDONLY|O_CLOEXEC) = 3
fstat(3, {st_mode=S_IFREG|0644, st_size=127, ...}) = 0
fstat(3, {st_mode=S_IFREG|0644, st_size=127, ...}) = 0
read(3, "TZif2\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\1\0\0\0\1\0\0\0\0"..., 4096) = 127
lseek(3, -71, SEEK_CUR)                 = 56
read(3, "TZif2\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\1\0\0\0\1\0\0\0\0"..., 4096) = 71
close(3)                                = 0
write(2, "--2020-01-31 20:00:05--  http://"..., 51--2020-01-31 20:00:05--  http://norvig.com/big.txt
) = 51
stat("/home/vagrant/.netrc", 0x7ffcc9408550) = -1 ENOENT (No such file or directory)
open("/usr/share/locale/locale.alias", O_RDONLY|O_CLOEXEC) = 3
fstat(3, {st_mode=S_IFREG|0644, st_size=2995, ...}) = 0
read(3, "# Locale name alias data base.\n#"..., 4096) = 2995
read(3, "", 4096)                       = 0
close(3)                                = 0
open("/usr/share/locale/en_US.UTF-8/LC_MESSAGES/wget.mo", O_RDONLY) = -1 ENOENT (No such file or directory)
open("/usr/share/locale/en_US.utf8/LC_MESSAGES/wget.mo", O_RDONLY) = -1 ENOENT (No such file or directory)
open("/usr/share/locale/en_US/LC_MESSAGES/wget.mo", O_RDONLY) = -1 ENOENT (No such file or directory)
open("/usr/share/locale/en.UTF-8/LC_MESSAGES/wget.mo", O_RDONLY) = -1 ENOENT (No such file or directory)
open("/usr/share/locale/en.utf8/LC_MESSAGES/wget.mo", O_RDONLY) = -1 ENOENT (No such file or directory)
open("/usr/share/locale/en/LC_MESSAGES/wget.mo", O_RDONLY) = -1 ENOENT (No such file or directory)
open("/usr/share/locale-langpack/en_US.UTF-8/LC_MESSAGES/wget.mo", O_RDONLY) = -1 ENOENT (No such file or directory)
open("/usr/share/locale-langpack/en_US.utf8/LC_MESSAGES/wget.mo", O_RDONLY) = -1 ENOENT (No such file or directory)
open("/usr/share/locale-langpack/en_US/LC_MESSAGES/wget.mo", O_RDONLY) = -1 ENOENT (No such file or directory)
open("/usr/share/locale-langpack/en.UTF-8/LC_MESSAGES/wget.mo", O_RDONLY) = -1 ENOENT (No such file or directory)
open("/usr/share/locale-langpack/en.utf8/LC_MESSAGES/wget.mo", O_RDONLY) = -1 ENOENT (No such file or directory)
open("/usr/share/locale-langpack/en/LC_MESSAGES/wget.mo", O_RDONLY) = -1 ENOENT (No such file or directory)
write(2, "Resolving norvig.com (norvig.com"..., 37Resolving norvig.com (norvig.com)... ) = 37
socket(PF_LOCAL, SOCK_STREAM|SOCK_CLOEXEC|SOCK_NONBLOCK, 0) = 3
connect(3, {sa_family=AF_LOCAL, sun_path="/var/run/nscd/socket"}, 110) = -1 ENOENT (No such file or directory)
close(3)                                = 0
socket(PF_LOCAL, SOCK_STREAM|SOCK_CLOEXEC|SOCK_NONBLOCK, 0) = 3
connect(3, {sa_family=AF_LOCAL, sun_path="/var/run/nscd/socket"}, 110) = -1 ENOENT (No such file or directory)
close(3)                                = 0
open("/etc/nsswitch.conf", O_RDONLY|O_CLOEXEC) = 3
fstat(3, {st_mode=S_IFREG|0644, st_size=497, ...}) = 0
read(3, "# /etc/nsswitch.conf\n#\n# Example"..., 4096) = 497
read(3, "", 4096)                       = 0
close(3)                                = 0
open("/etc/host.conf", O_RDONLY|O_CLOEXEC) = 3
fstat(3, {st_mode=S_IFREG|0644, st_size=92, ...}) = 0
read(3, "# The \"order\" line is only used "..., 4096) = 92
read(3, "", 4096)                       = 0
close(3)                                = 0
futex(0x7fc2d3c86a64, FUTEX_WAKE_PRIVATE, 2147483647) = 0
open("/etc/resolv.conf", O_RDONLY|O_CLOEXEC) = 3
fstat(3, {st_mode=S_IFREG|0644, st_size=171, ...}) = 0
read(3, "# Dynamic resolv.conf(5) file fo"..., 4096) = 171
read(3, "", 4096)                       = 0
close(3)                                = 0
uname({sysname="Linux", nodename="ubuntu-xenial", ...}) = 0
open("/etc/ld.so.cache", O_RDONLY|O_CLOEXEC) = 3
fstat(3, {st_mode=S_IFREG|0644, st_size=21118, ...}) = 0
mmap(NULL, 21118, PROT_READ, MAP_PRIVATE, 3, 0) = 0x7fc2d4e17000
close(3)                                = 0
access("/etc/ld.so.nohwcap", F_OK)      = -1 ENOENT (No such file or directory)
open("/lib/x86_64-linux-gnu/libnss_files.so.2", O_RDONLY|O_CLOEXEC) = 3
read(3, "\177ELF\2\1\1\0\0\0\0\0\0\0\0\0\3\0>\0\1\0\0\0\260!\0\0\0\0\0\0"..., 832) = 832
fstat(3, {st_mode=S_IFREG|0644, st_size=47600, ...}) = 0
mmap(NULL, 2168600, PROT_READ|PROT_EXEC, MAP_PRIVATE|MAP_DENYWRITE, 3, 0) = 0x7fc2d328b000
mprotect(0x7fc2d3296000, 2093056, PROT_NONE) = 0
mmap(0x7fc2d3495000, 8192, PROT_READ|PROT_WRITE, MAP_PRIVATE|MAP_FIXED|MAP_DENYWRITE, 3, 0xa000) = 0x7fc2d3495000
mmap(0x7fc2d3497000, 22296, PROT_READ|PROT_WRITE, MAP_PRIVATE|MAP_FIXED|MAP_ANONYMOUS, -1, 0) = 0x7fc2d3497000
close(3)                                = 0
mprotect(0x7fc2d3495000, 4096, PROT_READ) = 0
munmap(0x7fc2d4e17000, 21118)           = 0
open("/etc/hosts", O_RDONLY|O_CLOEXEC)  = 3
fstat(3, {st_mode=S_IFREG|0644, st_size=260, ...}) = 0
read(3, "127.0.0.1\tlocalhost\n\n# The follo"..., 4096) = 260
read(3, "", 4096)                       = 0
close(3)                                = 0
open("/etc/ld.so.cache", O_RDONLY|O_CLOEXEC) = 3
fstat(3, {st_mode=S_IFREG|0644, st_size=21118, ...}) = 0
mmap(NULL, 21118, PROT_READ, MAP_PRIVATE, 3, 0) = 0x7fc2d4e17000
close(3)                                = 0
access("/etc/ld.so.nohwcap", F_OK)      = -1 ENOENT (No such file or directory)
open("/lib/x86_64-linux-gnu/libnss_dns.so.2", O_RDONLY|O_CLOEXEC) = 3
read(3, "\177ELF\2\1\1\0\0\0\0\0\0\0\0\0\3\0>\0\1\0\0\0\220\17\0\0\0\0\0\0"..., 832) = 832
fstat(3, {st_mode=S_IFREG|0644, st_size=27000, ...}) = 0
mmap(NULL, 2121944, PROT_READ|PROT_EXEC, MAP_PRIVATE|MAP_DENYWRITE, 3, 0) = 0x7fc2d3084000
mprotect(0x7fc2d3089000, 2097152, PROT_NONE) = 0
mmap(0x7fc2d3289000, 8192, PROT_READ|PROT_WRITE, MAP_PRIVATE|MAP_FIXED|MAP_DENYWRITE, 3, 0x5000) = 0x7fc2d3289000
close(3)                                = 0
access("/etc/ld.so.nohwcap", F_OK)      = -1 ENOENT (No such file or directory)
open("/lib/x86_64-linux-gnu/libresolv.so.2", O_RDONLY|O_CLOEXEC) = 3
read(3, "\177ELF\2\1\1\0\0\0\0\0\0\0\0\0\3\0>\0\1\0\0\0P9\0\0\0\0\0\0"..., 832) = 832
fstat(3, {st_mode=S_IFREG|0644, st_size=101200, ...}) = 0
mmap(NULL, 2206280, PROT_READ|PROT_EXEC, MAP_PRIVATE|MAP_DENYWRITE, 3, 0) = 0x7fc2d2e69000
mprotect(0x7fc2d2e80000, 2097152, PROT_NONE) = 0
mmap(0x7fc2d3080000, 8192, PROT_READ|PROT_WRITE, MAP_PRIVATE|MAP_FIXED|MAP_DENYWRITE, 3, 0x17000) = 0x7fc2d3080000
mmap(0x7fc2d3082000, 6728, PROT_READ|PROT_WRITE, MAP_PRIVATE|MAP_FIXED|MAP_ANONYMOUS, -1, 0) = 0x7fc2d3082000
close(3)                                = 0
mprotect(0x7fc2d3080000, 4096, PROT_READ) = 0
mprotect(0x7fc2d3289000, 4096, PROT_READ) = 0
munmap(0x7fc2d4e17000, 21118)           = 0
stat("/etc/resolv.conf", {st_mode=S_IFREG|0644, st_size=171, ...}) = 0
open("/etc/resolv.conf", O_RDONLY|O_CLOEXEC) = 3
fstat(3, {st_mode=S_IFREG|0644, st_size=171, ...}) = 0
read(3, "# Dynamic resolv.conf(5) file fo"..., 4096) = 171
read(3, "", 4096)                       = 0
close(3)                                = 0
uname({sysname="Linux", nodename="ubuntu-xenial", ...}) = 0
socket(PF_INET, SOCK_DGRAM|SOCK_NONBLOCK, IPPROTO_IP) = 3
connect(3, {sa_family=AF_INET, sin_port=htons(53), sin_addr=inet_addr("10.0.2.3")}, 16) = 0
poll([{fd=3, events=POLLOUT}], 1, 0)    = 1 ([{fd=3, revents=POLLOUT}])
sendmmsg(3, {{{msg_name(0)=NULL, msg_iov(1)=[{"\201'\1\0\0\1\0\0\0\0\0\0\6norvig\3com\0\0\1\0\1", 28}], msg_controllen=0, msg_flags=0}, 28}, {{msg_name(0)=NULL, msg_iov(1)=[{"vJ\1\0\0\1\0\0\0\0\0\0\6norvig\3com\0\0\34\0\1", 28}], msg_controllen=0, msg_flags=MSG_TRUNC|MSG_EOR|MSG_FIN|MSG_MORE|MSG_WAITFORONE|MSG_CMSG_CLOEXEC|0x8a940010}, 28}}, 2, MSG_NOSIGNAL) = 2
poll([{fd=3, events=POLLIN}], 1, 5000)  = 1 ([{fd=3, revents=POLLIN}])
ioctl(3, FIONREAD, [44])                = 0
recvfrom(3, "\201'\201\200\0\1\0\1\0\0\0\0\6norvig\3com\0\0\1\0\1\300\f\0\1"..., 2048, 0, {sa_family=AF_INET, sin_port=htons(53), sin_addr=inet_addr("10.0.2.3")}, [16]) = 44
poll([{fd=3, events=POLLIN}], 1, 4997)  = 1 ([{fd=3, revents=POLLIN}])
ioctl(3, FIONREAD, [90])                = 0
recvfrom(3, "vJ\201\200\0\1\0\0\0\1\0\0\6norvig\3com\0\0\34\0\1\300\f\0\6"..., 65536, 0, {sa_family=AF_INET, sin_port=htons(53), sin_addr=inet_addr("10.0.2.3")}, [16]) = 90
close(3)                                = 0
write(2, "158.106.138.13", 14158.106.138.13)          = 14
write(2, "\n", 1
)                       = 1
write(2, "Connecting to norvig.com (norvig"..., 60Connecting to norvig.com (norvig.com)|158.106.138.13|:80... ) = 60
socket(PF_INET, SOCK_STREAM, IPPROTO_IP) = 3
connect(3, {sa_family=AF_INET, sin_port=htons(80), sin_addr=inet_addr("158.106.138.13")}, 16) = 0
write(2, "connected.\n", 11connected.
)            = 11
select(4, NULL, [3], NULL, {900, 0})    = 1 (out [3], left {899, 999997})
write(3, "GET /big.txt HTTP/1.1\r\nUser-Agen"..., 144) = 144
write(2, "HTTP request sent, awaiting resp"..., 40HTTP request sent, awaiting response... ) = 40
select(4, [3], NULL, NULL, {900, 0})    = 1 (in [3], left {899, 878069})
recvfrom(3, "HTTP/1.1 200 OK\r\nLast-Modified: "..., 511, MSG_PEEK, NULL, NULL) = 218
read(3, "HTTP/1.1 200 OK\r\nLast-Modified: "..., 218) = 218
write(2, "200 OK\n", 7200 OK
)                 = 7
stat("big.txt.11", 0x7ffcc9408580)      = -1 ENOENT (No such file or directory)
write(2, "Length: ", 8Length: )                 = 8
write(2, "6488666", 76488666)                  = 7
write(2, " (6.2M)", 7 (6.2M))                  = 7
write(2, " [text/plain]\n", 14 [text/plain]
)         = 14
open("big.txt.11", O_WRONLY|O_CREAT|O_TRUNC, 0666) = 4
open("/usr/lib/charset.alias", O_RDONLY|O_NOFOLLOW) = -1 ENOENT (No such file or directory)
write(2, "Saving to: \342\200\230big.txt.11\342\200\231\n", 28Saving to: big.txt.11
) = 28
ioctl(2, TIOCGWINSZ, {ws_row=30, ws_col=120, ws_xpixel=640, ws_ypixel=480}) = 0
write(2, "\n", 1
)                       = 1
open("/usr/lib/x86_64-linux-gnu/gconv/gconv-modules.cache", O_RDONLY) = 5
fstat(5, {st_mode=S_IFREG|0644, st_size=26258, ...}) = 0
mmap(NULL, 26258, PROT_READ, MAP_SHARED, 5, 0) = 0x7fc2d4c71000
close(5)                                = 0
futex(0x7fc2d3c838c8, FUTEX_WAKE_PRIVATE, 2147483647) = 0
)                       = 1
write(2, "big.txt.11                      "..., 119big.txt.11                      0%[                                                  ]       0  --.-KB/s               ) = 119
select(4, [3], NULL, NULL, {0, 950000}) = 1 (in [3], left {0, 949615})
read(3, "The Project Gutenberg EBook of T"..., 8192) = 2700

[q2 (3pts)] How many bytes does wget typically attempts to read from the file at a time typically (e.g., the buffer size)? Justify your answer by giving in your report one line of the strace output as an example.
strace -e trace=read http://norvig.com/big.txt /
wget attempts to read 8192 bytes at a time.
ead(3, "The Project Gutenberg EBook of T"..., 8192) = 1350
read(3, " by Jose Menendez)\n\n\n\nTHE ADVENT"..., 8192) = 8192

[q3 (3pts)] How many times does wget attempt to read pieces of file content, in total? Explain how you made that determination.
825 times. By looking at the summary of the system calls, read is attempted 825 times.
strace -c wget http://norvig.com/big.txt /
--2020-01-31 05:09:07--  http://norvig.com/big.txt
Resolving norvig.com (norvig.com)... 158.106.138.13
Connecting to norvig.com (norvig.com)|158.106.138.13|:80... connected.
HTTP request sent, awaiting response... 200 OK
Length: 6488666 (6.2M) [text/plain]
Saving to: big.txt

big.txt                           100%[============================================================>]   6.19M  2.68MB/s    in 2.3s

2020-01-31 05:09:09 (2.68 MB/s) - big.txt saved [6488666/6488666]

/: Scheme missing.
FINISHED --2020-01-31 05:09:09--
Total wall clock time: 2.8s
Downloaded: 1 files, 6.2M in 2.3s (2.68 MB/s)
% time     seconds  usecs/call     calls    errors syscall
------ ----------- ----------- --------- --------- ----------------
 37.55    0.011278           7      1632           write
 32.39    0.009729          12       825           read
 30.05    0.009026          11       797           select
  0.00    0.000000           0        39        13 open
  0.00    0.000000           0        29           close
  0.00    0.000000           0        10         6 stat
  0.00    0.000000           0        27           fstat
  0.00    0.000000           0         3           poll
  0.00    0.000000           0         1           lseek
  0.00    0.000000           0        40           mmap
  0.00    0.000000           0        26           mprotect
  0.00    0.000000           0         3           munmap
  0.00    0.000000           0         3           brk
  0.00    0.000000           0         7           rt_sigaction
  0.00    0.000000           0         1           rt_sigprocmask
  0.00    0.000000           0         5           ioctl
  0.00    0.000000           0        14        14 access
  0.00    0.000000           0         4           socket
  0.00    0.000000           0         4         2 connect
  0.00    0.000000           0         3           recvfrom
  0.00    0.000000           0         1           execve
  0.00    0.000000           0         2           uname
  0.00    0.000000           0         1           getrlimit
  0.00    0.000000           0         1           utime
  0.00    0.000000           0         1           statfs
  0.00    0.000000           0         1           arch_prctl
  0.00    0.000000           0         2           futex
  0.00    0.000000           0         1           set_tid_address
  0.00    0.000000           0         2           clock_getres
  0.00    0.000000           0         1           set_robust_list
  0.00    0.000000           0         1           sendmmsg
------ ----------- ----------- --------- --------- ----------------
100.00    0.030033                  3487        35 total

[q4 (3pts)] Out of these, how many times the read system call does NOT receive the number of bytes it wants? Explain how you made this determination.
29 of 825 times, by examining the output, the following attempts to read are less than 8192 bytes.
strace -e trace=read wget http://norvig.com/big.txt /
read(3, "\177ELF\2\1\1\0\0\0\0\0\0\0\0\0\3\0>\0\1\0\0\0000\25\0\0\0\0\0\0"..., 832) = 832
read(3, "\177ELF\2\1\1\0\0\0\0\0\0\0\0\0\3\0>\0\1\0\0\0\20\25\0\0\0\0\0\0"..., 832) = 832
read(3, "\177ELF\2\1\1\0\0\0\0\0\0\0\0\0\3\0>\0\1\0\0\0\320^\1\0\0\0\0\0"..., 832) = 832
read(3, "\177ELF\2\1\1\0\0\0\0\0\0\0\0\0\3\0>\0\1\0\0\0\0\36\6\0\0\0\0\0"..., 832) = 832
read(3, "\177ELF\2\1\1\0\0\0\0\0\0\0\0\0\3\0>\0\1\0\0\0\260\35\0\0\0\0\0\0"..., 832) = 832
read(3, "\177ELF\2\1\1\0\0\0\0\0\0\0\0\0\3\0>\0\1\0\0\0p+\0\0\0\0\0\0"..., 832) = 832
read(3, "\177ELF\2\1\1\3\0\0\0\0\0\0\0\0\3\0>\0\1\0\0\0P\t\2\0\0\0\0\0"..., 832) = 832
read(3, "\177ELF\2\1\1\0\0\0\0\0\0\0\0\0\3\0>\0\1\0\0\0\260`\0\0\0\0\0\0"..., 832) = 832
read(3, "\177ELF\2\1\1\0\0\0\0\0\0\0\0\0\3\0>\0\1\0\0\0\240\r\0\0\0\0\0\0"..., 832) = 832
read(3, "###\n### Sample Wget initializati"..., 4096) = 4096
read(3, "ruct = off\n\n# You can turn on re"..., 4096) = 846
read(3, "", 4096)                       = 0
read(3, "TZif2\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\1\0\0\0\1\0\0\0\0"..., 4096) = 127
read(3, "TZif2\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\1\0\0\0\1\0\0\0\0"..., 4096) = 71
--2020-01-31 20:16:38--  http://norvig.com/big.txt
read(3, "# Locale name alias data base.\n#"..., 4096) = 2995
read(3, "", 4096)                       = 0
Resolving norvig.com (norvig.com)... read(3, "# /etc/nsswitch.conf\n#\n# Example"..., 4096) = 497
read(3, "", 4096)                       = 0
read(3, "# The \"order\" line is only used "..., 4096) = 92
read(3, "", 4096)                       = 0
read(3, "# Dynamic resolv.conf(5) file fo"..., 4096) = 171
read(3, "", 4096)                       = 0
read(3, "\177ELF\2\1\1\0\0\0\0\0\0\0\0\0\3\0>\0\1\0\0\0\260!\0\0\0\0\0\0"..., 832) = 832
read(3, "127.0.0.1\tlocalhost\n\n# The follo"..., 4096) = 260
read(3, "", 4096)                       = 0
read(3, "\177ELF\2\1\1\0\0\0\0\0\0\0\0\0\3\0>\0\1\0\0\0\220\17\0\0\0\0\0\0"..., 832) = 832
read(3, "\177ELF\2\1\1\0\0\0\0\0\0\0\0\0\3\0>\0\1\0\0\0P9\0\0\0\0\0\0"..., 832) = 832
read(3, "# Dynamic resolv.conf(5) file fo"..., 4096) = 171
read(3, "", 4096)                       = 0
read(3, "hoebe  father in law  conform  p"..., 5966) = 5966

[q5 (2pts)] Do you conclude that wget typically fills its buffer or not?
The wget for this file does fill its buffer.

[q6 (3pts)] How many times does wget attempt to read pieces of file content, in total?
823 times.
~$ strace -c wget http://dirt01.ics.hawaii.edu/~henric/big.txt /
--2020-01-31 05:15:41--  http://dirt01.ics.hawaii.edu/~henric/big.txt
Resolving dirt01.ics.hawaii.edu (dirt01.ics.hawaii.edu)... 128.171.140.101
Connecting to dirt01.ics.hawaii.edu (dirt01.ics.hawaii.edu)|128.171.140.101|:80... connected.
HTTP request sent, awaiting response... 200 OK
Length: 6488666 (6.2M) [text/plain]
Saving to: big.txt.2

big.txt.2                                  100%[======================================================================================>]   6.19M  4.28MB/s    in 1.4s

2020-01-31 05:15:43 (4.28 MB/s) - big.txt.2 saved [6488666/6488666]

/: Scheme missing.
FINISHED --2020-01-31 05:15:43--
Total wall clock time: 1.5s
Downloaded: 1 files, 6.2M in 1.4s (4.28 MB/s)
% time     seconds  usecs/call     calls    errors syscall
------ ----------- ----------- --------- --------- ----------------
 51.71    0.008334          10       823           read
 44.26    0.007133           4      1622           write
  4.03    0.000650           1       795           select
  0.00    0.000000           0        39        13 open
  0.00    0.000000           0        29           close
  0.00    0.000000           0        12         6 stat
  0.00    0.000000           0        27           fstat
  0.00    0.000000           0         3           poll
  0.00    0.000000           0         1           lseek
  0.00    0.000000           0        40           mmap
  0.00    0.000000           0        26           mprotect
  0.00    0.000000           0         3           munmap
  0.00    0.000000           0         3           brk
  0.00    0.000000           0         7           rt_sigaction
  0.00    0.000000           0         1           rt_sigprocmask
  0.00    0.000000           0         5           ioctl
  0.00    0.000000           0        14        14 access
  0.00    0.000000           0         4           socket
  0.00    0.000000           0         4         2 connect
  0.00    0.000000           0         3           recvfrom
  0.00    0.000000           0         1           execve
  0.00    0.000000           0         2           uname
  0.00    0.000000           0         1           getrlimit
  0.00    0.000000           0         1           utime
  0.00    0.000000           0         1           statfs
  0.00    0.000000           0         1           arch_prctl
  0.00    0.000000           0         2           futex
  0.00    0.000000           0         1           set_tid_address
  0.00    0.000000           0         2           clock_getres
  0.00    0.000000           0         1           set_robust_list
  0.00    0.000000           0         1           sendmmsg
------ ----------- ----------- --------- --------- ----------------
100.00    0.016117                  3475        35 total

[q7 (3pts)] Out of these, how many times read does NOT receive the number of bytes it wants?
30 of 823 times.
strace -e trace=read wget  http://dirt01.ics.hawaii.edu/~henric/big.txt /
read(3, "\177ELF\2\1\1\0\0\0\0\0\0\0\0\0\3\0>\0\1\0\0\0000\25\0\0\0\0\0\0"..., 832) = 832
read(3, "\177ELF\2\1\1\0\0\0\0\0\0\0\0\0\3\0>\0\1\0\0\0\20\25\0\0\0\0\0\0"..., 832) = 832
read(3, "\177ELF\2\1\1\0\0\0\0\0\0\0\0\0\3\0>\0\1\0\0\0\320^\1\0\0\0\0\0"..., 832) = 832
read(3, "\177ELF\2\1\1\0\0\0\0\0\0\0\0\0\3\0>\0\1\0\0\0\0\36\6\0\0\0\0\0"..., 832) = 832
read(3, "\177ELF\2\1\1\0\0\0\0\0\0\0\0\0\3\0>\0\1\0\0\0\260\35\0\0\0\0\0\0"..., 832) = 832
read(3, "\177ELF\2\1\1\0\0\0\0\0\0\0\0\0\3\0>\0\1\0\0\0p+\0\0\0\0\0\0"..., 832) = 832
read(3, "\177ELF\2\1\1\3\0\0\0\0\0\0\0\0\3\0>\0\1\0\0\0P\t\2\0\0\0\0\0"..., 832) = 832
read(3, "\177ELF\2\1\1\0\0\0\0\0\0\0\0\0\3\0>\0\1\0\0\0\260`\0\0\0\0\0\0"..., 832) = 832
read(3, "\177ELF\2\1\1\0\0\0\0\0\0\0\0\0\3\0>\0\1\0\0\0\240\r\0\0\0\0\0\0"..., 832) = 832
read(3, "###\n### Sample Wget initializati"..., 4096) = 4096
read(3, "ruct = off\n\n# You can turn on re"..., 4096) = 846
read(3, "", 4096)                       = 0
read(3, "TZif2\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\1\0\0\0\1\0\0\0\0"..., 4096) = 127
read(3, "TZif2\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\0\1\0\0\0\1\0\0\0\0"..., 4096) = 71
--2020-01-31 05:16:56--  http://dirt01.ics.hawaii.edu/~henric/big.txt
read(3, "# Locale name alias data base.\n#"..., 4096) = 2995
read(3, "", 4096)                       = 0
Resolving dirt01.ics.hawaii.edu (dirt01.ics.hawaii.edu)... read(3, "# /etc/nsswitch.conf\n#\n# Example"..., 4096) = 497
read(3, "", 4096)                       = 0
read(3, "# The \"order\" line is only used "..., 4096) = 92
read(3, "", 4096)                       = 0
read(3, "# Dynamic resolv.conf(5) file fo"..., 4096) = 171
read(3, "", 4096)                       = 0
read(3, "\177ELF\2\1\1\0\0\0\0\0\0\0\0\0\3\0>\0\1\0\0\0\260!\0\0\0\0\0\0"..., 832) = 832
read(3, "127.0.0.1\tlocalhost\n\n# The follo"..., 4096) = 260
read(3, "", 4096)                       = 0
read(3, "\177ELF\2\1\1\0\0\0\0\0\0\0\0\0\3\0>\0\1\0\0\0\220\17\0\0\0\0\0\0"..., 832) = 832
read(3, "\177ELF\2\1\1\0\0\0\0\0\0\0\0\0\3\0>\0\1\0\0\0P9\0\0\0\0\0\0"..., 832) = 832
read(3, "# Dynamic resolv.conf(5) file fo"..., 4096) = 171
read(3, "", 4096)                       = 0
128.171.140.101
Connecting to dirt01.ics.hawaii.edu (dirt01.ics.hawaii.edu)|128.171.140.101|:80... connected.
HTTP request sent, awaiting response... read(3, "HTTP/1.1 200 OK\r\nDate: Fri, 31 J"..., 316) = 316
200 OK
Length: 6488666 (6.2M) [text/plain]
Saving to: big.txt.3

big.txt.3                                    0%[                                                                                       ]       0  --.-KB/s               read(3, "The Project Gutenberg EBook of T"..., 8192) = 8192
read(3, "ite so! You have not observed. A"..., 8192) = 8192
read(3, "myself in his power. I have come"..., 8192) = 8192
read(3, " day, and returns at seven sharp"..., 8192) = 8192
read(3, "ed. It was not merely that Holme"..., 8192) = 8192
read(3, "t out. When I cried out that it "..., 8192) = 8192
read(3, "ance from his small fat-encircle"..., 8192) = 8192
read(3, ", I presume?\"\n\n\"Yes, sir. He and"..., 8192) = 8192
read(3, "nominal.'\n\n\" 'What do you call p"..., 8192) = 8192
read(3, " is the less mysterious it prove"..., 8192) = 8192
read(3, "r was a long, thin, sad-faced ma"..., 8192) = 8192
read(3, "Jones from the thin, sighing not"..., 8192) = 8192
read(3, "onclusions most stale and unprof"..., 8192) = 8192
read(3, "e interest.\"\n\n\"You interest me e"..., 8192) = 8192
read(3, "him, there might be some reason,"..., 8192) = 8192
read(3, "e case of the King of Bohemia an"..., 8192) = 8192
read(3, "urning towards anyone else. But "..., 8192) = 8192
read(3, "ed lives, though both the McCart"..., 8192) = 8192
read(3, "inted out the paragraph in which"..., 8192) = 8192
read(3, "es; but he is too tender-hearted"..., 8192) = 8192
read(3, "young McCarthy for all that he h"..., 8192) = 8192
read(3, "ed up in the wood.\n\n\"This may in"..., 8192) = 8192
read(3, "per before him. \"Just tell us th"..., 8192) = 8192
read(3, "ingular adventures of the Grice "..., 8192) = 8192
read(3, " upon the inner flap, just above"..., 8192) = 8192
read(3, "e lived happily at Horsham, and "..., 8192) = 8192
read(3, ",\" he said, \"I say now, as I sai"..., 8192) = 8192
read(3, "arkness he missed his path and w"..., 8192) = 8192
read(3, " was sure of it, at the Bar of G"..., 8192) = 8192
read(3, " chronicler still more so. My ro"..., 8192) = 8192
read(3, " was one of absolute ignorance, "..., 8192) = 8192
read(3, "ing that there were two of us, s"..., 8192) = 8192
read(3, "different man to the sombre thin"..., 8192) = 8192
read(3, "e as possible I made a good scar"..., 8192) = 8192
read(3, " front of him he saw, in the gas"..., 8192) = 8192
read(3, "with astonishment.\n\n\"The goose, "..., 8192) = 8192
read(3, ". It is a cold night, and I obse"..., 8192) = 8192
read(3, " country folk, and the numbers a"..., 8192) = 8192
read(3, "le enough of this poor Horner in"..., 8192) = 8192
read(3, "and I on you.\"\n\n\"What is it, the"..., 8192) = 8192
read(3, "another public exposure. He had "..., 8192) = 8192
read(3, " a man who is far from being sat"..., 8192) = 8192
read(3, "is beauty would have had a mere "..., 8192) = 8192
read(3, "ator into another room, when, wi"..., 8192) = 8192
read(3, "ter we were out on the dark road"..., 8192) = 8192
read(3, "covered by any chemical test was"..., 8192) = 8192
read(3, "s you had better not speak of th"..., 8192) = 8192
read(3, "inion from the very heads of you"..., 8192) = 8192
read(3, "ling of uneasiness began to stea"..., 8192) = 8192
read(3, ", rough surface. Then it flashed"..., 8192) = 8192
read(3, "es quietly.\n\n\"Really, now!\" crie"..., 8192) = 8192
read(3, "e had nothing else to do.\"\n\n\"It "..., 8192) = 8192
read(3, "any. On ascertaining that his da"..., 8192) = 8192
read(3, " else to think of.\"\n\n\"You did no"..., 8192) = 7642
read(3, "the dress is a pocket. In the po"..., 8192) = 8192
read(3, "news came for a year and more, s"..., 8192) = 2896
read(3, "ed the American. \"It gave the na"..., 8192) = 8192
read(3, "ouses.\"\n\n\"I believe that he is c"..., 8192) = 8192
read(3, "d a cab and drove out to my hous"..., 8192) = 8192
read(3, "you trying to tear off another p"..., 8192) = 8192
read(3, "st the absolute pallor of her sk"..., 8192) = 8192
read(3, " the fireplace. \"I only wish tha"..., 8192) = 8192
read(3, "uld see what passed in the hall "..., 8192) = 8192
read(3, " safely say,\" returned Holmes, \""..., 8192) = 8192
read(3, "s?'\n\n\" 'My accomplishments, sir,"..., 8192) = 8192
read(3, " with you. I assure you that you"..., 8192) = 8192
read(3, "on I have gathered that they hav"..., 8192) = 8192
read(3, "orth.'\n\n\"The warning was no idle"..., 8192) = 8192
read(3, "n, of the woman, of the servants"..., 8192) = 8192
read(3, "ed, into the house. We laid him "..., 8192) = 8192
read(3, "ese are the only ones\nthat have "..., 8192) = 8192
read(3, "e) your annual (or equivalent\n  "..., 8192) = 8192
read(3, "ican People_\nDODD, W.E. _Expansi"..., 8192) = 8192
read(3, "       515\n          The Rooseve"..., 8192) = 8192
read(3, " grant of land, and its trading "..., 8192) = 8192
read(3, ".\n\nNew England was almost purely"..., 8192) = 8192
read(3, "ung as tenaciously to their moth"..., 8192) = 8192
read(3, "on block intended to establish a"..., 8192) = 8192
read(3, "te plot of his own. The motto of"..., 8192) = 8192
read(3, "outh, as soon as\na foothold was "..., 8192) = 8192
read(3, "e, and hemp for rope\nmade the wa"..., 8192) = 8192
read(3, "Carolinas.\n\n=Growth of Towns.=--"..., 8192) = 8192
read(3, "ly tolerated, they pressed stead"..., 8192) = 8192
read(3, "asters of servants to be diligen"..., 8192) = 8192
read(3, "of a similar character he was th"..., 8192) = 8192
read(3, "to\nforce the governor to sign bi"..., 8192) = 8192
read(3, "n motion destructive forces of t"..., 8192) = 8192
read(3, " hold\nmeetings until after the e"..., 8192) = 8192
read(3, "es where Andros had been operati"..., 8192) = 8192
read(3, "ods from the colonies and even t"..., 8192) = 8192
read(3, "f national unity. Most of the\nco"..., 8192) = 8192
read(3, "s for controlling the entire gov"..., 8192) = 8192
read(3, "bonds,--on\nlicenses to practice "..., 8192) = 8192
read(3, "ct, Parliament did not admit the"..., 8192) = 8192
read(3, " agreed not to import British\ngo"..., 8192) = 8192
read(3, "Massachusetts. Reinforcements we"..., 8192) = 8192
read(3, "ons. It prepared an address to\nK"..., 8192) = 8192
read(3, "to matters of government, \"it is"..., 8192) = 8192
read(3, "cers fled in haste,\nand it becam"..., 8192) = 8192
read(3, "tion and that\n\"many more than fo"..., 8192) = 8192
read(3, "the Revolution. Only a small\npor"..., 8192) = 8192
read(3, " recruits and temperamental mili"..., 8192) = 8192
read(3, "r money to\nfinance the war. \"Do "..., 8192) = 8192
read(3, "dson\nand Delaware rivers were bl"..., 8192) = 8192
read(3, "tlement. This\nwas embarrassing. "..., 8192) = 8192
read(3, "ation of the Causes and the Nece"..., 8192) = 8192
read(3, "re defeated at the ensuing elect"..., 8192) = 8192
read(3, "y the Congress in summoning the "..., 8192) = 8192
read(3, " of\nslaves, moreover, it was fea"..., 8192) = 8192
read(3, "and\ncollect taxes, duties, impos"..., 8192) = 8192
read(3, "wspaper articles they discussed "..., 8192) = 8192
read(3, "ratified and became in 1791 a\npa"..., 8192) = 8192
read(3, "farms and plantations; by making"..., 8192) = 8192
read(3, "e hundred fifty years, a meeting"..., 8192) = 8192
read(3, "y criminal; of all villains, the"..., 8192) = 8192
read(3, "continued friendship. When the n"..., 8192) = 8192
read(3, "\n=The Burke-Paine Controversy.=-"..., 8192) = 8192
read(3, "ional\neconomy. The better educat"..., 8192) = 8192
read(3, "t of it. He prepared for the ope"..., 8192) = 8192
read(3, "e the prices of foreign goods\ndo"..., 8192) = 8192
read(3, "arching of American ships, or th"..., 8192) = 8192
read(3, "e with\nsympathy. Three of them, "..., 8192) = 8192
read(3, "ederalist principles from the Su"..., 8192) = 8192
read(3, " disaster.\n\nBy dint of much labo"..., 8192) = 8192
read(3, "lue of the far-away country unde"..., 8192) = 8192
read(3, "the Lake region. Sons and daught"..., 8192) = 8192
read(3, "nd of the government fell upon t"..., 8192) = 8192
read(3, " from the towns,--English,\nScotc"..., 8192) = 8192
read(3, "n who had hitherto accepted with"..., 8192) = 8192
read(3, "well-being of the community, int"..., 8192) = 8192
read(3, "oposals and unable to agree on\na"..., 8192) = 8192
read(3, "internal improvements. By\nsignin"..., 8192) = 8192
read(3, "o see eye to eye with the manufa"..., 8192) = 8192
read(3, "ound banks chartered by the stat"..., 8192) = 8192
read(3, "o eminence in the profession of "..., 8192) = 8192
read(3, "he aristocracy which George III "..., 8192) = 8192
read(3, "an Epoch_.\n\nC. Schurz, _Henry Cl"..., 8192) = 8192
read(3, "heroic days when French hunters "..., 8192) = 8192
read(3, "to states. As soon as a few thou"..., 8192) = 8192
read(3, "ction. President Polk, accepting"..., 8192) = 8192
read(3, "orny!\" rang the cry in\nmany a Ne"..., 8192) = 8192
read(3, "SUMMARY OF WESTERN DEVELOPMENT A"..., 8192) = 8192
read(3, "along the bed of the Atlantic, M"..., 8192) = 8192
read(3, "he employees of railroads, expre"..., 8192) = 8192
read(3, "nions, labor\nleaders, strikes, a"..., 8192) = 8192
read(3, " As\na result of the protective t"..., 8192) = 8192
read(3, "ced, on\nwhat grounds it is not n"..., 8192) = 8192
read(3, "popular but pernicious doctrine "..., 8192) = 8192
read(3, "vernment. \"A party,\" he said, \"i"..., 8192) = 8192
read(3, "of the secession to come.\n\n[Illu"..., 8192) = 8192
read(3, "ractical men, after all, the\n\"ru"..., 8192) = 8192
read(3, "es were offered; none with more "..., 8192) = 8192
read(3, "on Party.=--While the Democratic"..., 8192) = 8192
read(3, "lorida, Georgia, Alabama, Missis"..., 8192) = 8192
read(3, "d open aid\nfrom European powers "..., 8192) = 8192
read(3, " in \"greenbacks\"--paper money is"..., 8192) = 8192
read(3, "a\nproclamation of emancipation; "..., 8192) = 8192
read(3, "st equally unbroken series of mi"..., 8192) = 8192
read(3, "icted prices\ngave a stimulus to "..., 8192) = 8192
read(3, "saw fit to enact and until in it"..., 8192) = 8192
read(3, "o all negro\nmen, leaving the wom"..., 8192) = 8192
read(3, "l Studies.=--Lincoln, Davis, Lee"..., 8192) = 8192
read(3, "hold it for a few minutes. Frequ"..., 8192) = 8192
read(3, "e Court Refuses to Intervene.=--"..., 8192) = 8192
read(3, "he value of its farm crops\nincre"..., 8192) = 8192
read(3, "mong small centers prevented the"..., 8192) = 8192
read(3, ", _Studies in the American Race "..., 8192) = 8192
read(3, "om the wells of Pennsylvania in "..., 8192) = 8192
read(3, "hence came the founders of\nAmeri"..., 8192) = 8192
read(3, "ssary to safeguard the purity of"..., 8192) = 8192
read(3, " spoils system became more\nvirul"..., 8192) = 8192
read(3, "rty to victory.\n\n\n=References=\n\n"..., 8192) = 8192
read(3, "s of time. It was\npushed by men "..., 8192) = 8192
read(3, ", however, both had to go, as th"..., 8192) = 8192
read(3, "d by browsing\nsheep were broken "..., 8192) = 8192
read(3, "regon\ncompact made by the pionee"..., 8192) = 8192
read(3, "eatures\nof the frontier that wer"..., 8192) = 8192
read(3, "\n\n=References=\n\nHenry Inman, _Th"..., 8192) = 8192
read(3, "st\nthey advocated more paper not"..., 8192) = 8192
read(3, "r. Blocked by the Republican Sen"..., 8192) = 8192
read(3, "ct declared\nillegal \"every contr"..., 8192) = 8192
read(3, "r of his own party. In 1893 a gr"..., 8192) = 8192
read(3, "South and West had triumphed ove"..., 8192) = 8192
read(3, " authority: \"They are all allied"..., 8192) = 8192
read(3, "heir troops and left the field\nt"..., 8192) = 8192
read(3, "note none too conciliatory, aske"..., 8192) = 8192
read(3, "tions of democracy, naturally sy"..., 8192) = 8192
read(3, "med force in securing peace and\n"..., 8192) = 8192
read(3, " men freshly elected in the stir"..., 8192) = 8192
read(3, " time carried\non a campaign of h"..., 8192) = 8192
read(3, "te the Secretary of State, John "..., 8192) = 8192
read(3, "ting\nPresident Roosevelt on the "..., 8192) = 8192
read(3, "ven to the practice among Europe"..., 8192) = 8192
read(3, "l considerations,\nrecognized the"..., 8192) = 8192
read(3, "tolen to build up colossal fortu"..., 8192) = 8192
read(3, "re rumors of a Republican moveme"..., 8192) = 8192
read(3, "use of\nRepresentatives and began"..., 8192) = 8192
read(3, "\n\n1. Compare the early career of"..., 8192) = 8192
read(3, "ormers were, in a\nsense, engaged"..., 8192) = 8192
read(3, "rm of \"direct government.\"\n\nThes"..., 8192) = 8192
read(3, "t railways were not like other e"..., 8192) = 8192
read(3, "ars, thirty-five states had\nsimi"..., 8192) = 8192
read(3, "--a book that was destined to se"..., 8192) = 8192
read(3, "d in a\nDeclaration of Rights mod"..., 8192) = 8192
read(3, "ore some results. Many eminent\nc"..., 8192) = 8192
read(3, "ains in civil liberty for women."..., 8192) = 8192
read(3, "on members. By the opening of 19"..., 8192) = 8192
read(3, "lief in the establishment of coo"..., 8192) = 8192
read(3, "ongress in the Esch-Cummins rail"..., 8192) = 8192
read(3, " Effects on the United States_.\n"..., 8192) = 8192
read(3, "ls of the country.\n\n=Federal and"..., 8192) = 8192
read(3, "\nof the United States. In the di"..., 8192) = 8192
read(3, "wful, had to be effective.\n\nThes"..., 8192) = 8192
read(3, "publicans chose a middle\ncourse,"..., 8192) = 8192
read(3, "-thirds of a man's income by tax"..., 8192) = 8192
read(3, "ermans launched their grand driv"..., 8192) = 8192
read(3, "esentatives of the United States"..., 8192) = 8192
read(3, "en wrong-doing\non the part of ce"..., 8192) = 8192
read(3, "ch shall consist of a Senate and"..., 8192) = 8192
read(3, "the debts and provide for the\nco"..., 8192) = 8192
read(3, "t, declaring what\nofficer shall "..., 8192) = 8192
read(3, "s of the United States which sha"..., 8192) = 8192
read(3, "al officers of a State, or the m"..., 8192) = 8192
read(3, "the 16th Amendment, p. 640.\n\n[7]"..., 8192) = 8192
read(3, ".\n       4. Relations with the E"..., 8192) = 8192
read(3, "fferson Davis, Tilden, Cleveland"..., 8192) = 8192
read(3, " 208, 281, 321, 328\n\nCalifornia,"..., 8192) = 8192
read(3, "t\n\nLa Salle, 59\n\nLawrence, Capta"..., 8192) = 8192
read(3, "house reform, 549\n\nTennessee, 28"..., 8192) = 8192
read(3, "eferences to Project Gutenberg\na"..., 8192) = 8192
read(3, "h\nyour written explanation.  The"..., 8192) = 8192
read(3, "     |\n|                        "..., 8192) = 8192
read(3, "ulous Abscess in Lumbar Region  "..., 8192) = 8192
read(3, "      486\n\n  142. Multiple Chond"..., 8192) = 8192
read(3, "inds, the\nmost important being t"..., 8192) = 8192
read(3, "een described as the type of ide"..., 8192) = 8192
read(3, " influences the rate of healing;"..., 8192) = 8192
read(3, "affixed.\n\nAs a dressing for the "..., 8192) = 8192
read(3, "t is\nnecessary to eliminate vari"..., 8192) = 8192
read(3, "s than tissues clean-cut\nwith a "..., 8192) = 8192
read(3, "nd injection is given after an i"..., 8192) = 8192
read(3, ", the glanders\nbacillus, and the"..., 8192) = 8192
read(3, "of its action. If the natural pr"..., 8192) = 8192
read(3, " like\nperiosteum or bone, or in "..., 8192) = 8192
read(3, "ump, the rarefaction of the air "..., 8192) = 8192
read(3, "tment of inflammation in special"..., 8192) = 8192
read(3, "rough iron\nimplements from which"..., 8192) = 8192
read(3, "re the pus threatens to point, a"..., 8192) = 8192
read(3, "f the\ninflammation to the surfac"..., 8192) = 8192
read(3, " from\nappearing on the front. Th"..., 8192) = 8192
read(3, " the\nskin or to another mucous s"..., 8192) = 8192
read(3, "imes there is delirium.\n\nThe _pr"..., 8192) = 8192
read(3, "vascularity, and whether\nthey ar"..., 8192) = 8192
read(3, "he edges are slightly inflamed, "..., 8192) = 8192
read(3, "se ulcers which are _stationary_"..., 8192) = 8192
read(3, "phylaxis_ of these ulcers consis"..., 8192) = 8192
read(3, "ing the limb in\nan elevated posi"..., 8192) = 8192
read(3, "ia do not\nplay the most importan"..., 8192) = 8192
read(3, "the operation is borne\nremarkabl"..., 8192) = 8192
read(3, "ence is merely one of degree. Fr"..., 8192) = 8192
read(3, "ading rapidly, result in extensi"..., 8192) = 8192
read(3, " the room or even the house the\n"..., 8192) = 8192
read(3, "n hue\nfrom a bright scarlet to a"..., 8192) = 8192
read(3, "ent\nhas greatly diminished the n"..., 8192) = 8192
read(3, "may\nresult from spasm of the ure"..., 8192) = 8192
read(3, " and spinal cord should be\nretai"..., 8192) = 8192
read(3, "may\nsuggest the possibility of a"..., 8192) = 8192
read(3, "which has never been\nmet with ou"..., 8192) = 8192
read(3, "nated\nfemale flea remains attach"..., 8192) = 8192
read(3, "\nthe bronchial glands at the roo"..., 8192) = 8192
read(3, "s to this is that\nthe focus is o"..., 8192) = 8192
read(3, " or spine. It is called\nresidual"..., 8192) = 8192
read(3, "eminders_--Tertiary period: _Gen"..., 8192) = 8192
read(3, "ilis insontium_.\n\n#Stages or Per"..., 8192) = 8192
read(3, "n nearest relation to the\nsore a"..., 8192) = 8192
read(3, "ld be confined to bed.\n\nIn _conc"..., 8192) = 8192
read(3, "f they are\nnot recognised and tr"..., 8192) = 8192
read(3, "osition, with\na feeling of chill"..., 8192) = 8192
read(3, "der these conditions without ris"..., 8192) = 8192
read(3, "ch\nare liable to break down and "..., 8192) = 8192
read(3, "'s cry is\nhoarse.\n\n_Affections o"..., 8192) = 8192
read(3, "lin\nstated his belief that, whil"..., 8192) = 8192
read(3, " of their situation--for example"..., 8192) = 8192
read(3, "ium and\npresents two clinical va"..., 8192) = 8192
read(3, " certain\nhard dental tumours whi"..., 8192) = 8192
read(3, "hich appears to be encapsulated,"..., 8192) = 8192
read(3, "ither radium nor X-rays is avail"..., 8192) = 8192
read(3, " annular ringworm. The growing\ne"..., 8192) = 8192
read(3, "es are less amenable to ray trea"..., 8192) = 8192
read(3, "strangulation, an event which is"..., 8192) = 8192
read(3, "a\nmeshwork of fibrous tissue, th"..., 8192) = 8192
read(3, "e to be used with caution,\nas th"..., 8192) = 8192
read(3, " considerable\nportions of skin, "..., 8192) = 8192
read(3, "oke, or powder. These\nfeatures a"..., 8192) = 8192
read(3, "he fragments of fractured bones "..., 8192) = 8192
read(3, "ning and enlargement of the sple"..., 8192) = 8192
read(3, " enable the purification to be c"..., 8192) = 8192
read(3, "other way than by the knife of t"..., 8192) = 8192
read(3, "tream of\nrunning water as hot as"..., 8192) = 8192
read(3, "al growth.\n\nPads of _sphagnum mo"..., 8192) = 8192
read(3, "eaching the brain and causing sh"..., 8192) = 8192
read(3, "rium followed by coma ensues, an"..., 8192) = 8192
big.txt.3                                   38%[================================>                                                      ]   2.35M  11.7MB/s               read(3, "t of the muscular coat. The\nexte"..., 8192) = 8192
read(3, "bcutaneous rupture of the arteri"..., 8192) = 8192
read(3, "nd securing them with a\nligature"..., 8192) = 8192
read(3, "d\nretraction of the muscular coa"..., 8192) = 8192
read(3, " the removal of\nthe forceps at t"..., 8192) = 8192
read(3, "ages from a septic wound in the "..., 8192) = 8192
read(3, "r from the affection in its typi"..., 8192) = 8192
read(3, " The patient\nshould drink large "..., 8192) = 8192
read(3, "in arteries, because slowing\nof "..., 8192) = 8192
read(3, "ancy. The importance of the wear"..., 8192) = 8192
read(3, "tion of cases, especially in eld"..., 8192) = 8192
read(3, "ed by Electrolysis.]\n\n_Prognosis"..., 8192) = 8192
read(3, "s\nconvenient to describe in this"..., 8192) = 8192
read(3, "ed immediately.\n\nFluid swellings"..., 8192) = 8192
read(3, "igated near its bifurcation, or "..., 8192) = 8192
read(3, "erosion of these bones.\nPressure"..., 8192) = 8192
read(3, ".\n\n#Intracranial aneurysm# invol"..., 8192) = 8192
read(3, "in both limbs. It is generally d"..., 8192) = 8192
read(3, " with the\nthoracic duct, the ple"..., 8192) = 8192
read(3, " tissue--_peri-lymphangitis_.\nOn"..., 8192) = 8192
read(3, "ys.\n\nLymphangiomas are met with "..., 8192) = 8192
read(3, "g skin.\nThe further stages--redd"..., 8192) = 8192
read(3, "nlarged, and it is\nprobable that"..., 8192) = 8192
read(3, "ting statements as to the\nsensor"..., 8192) = 8192
read(3, "f the nerve, and are believed to"..., 8192) = 8192
read(3, "ear.\n\nIn cases of complete nerve"..., 8192) = 8192
read(3, "e diphtheritic form the\nsoft pal"..., 8192) = 8192
read(3, " the only treatment to be\nrecomm"..., 8192) = 8192
read(3, " for nerve\ninjuries in general. "..., 8192) = 8192
read(3, "hing, and electricity. If there "..., 8192) = 8192
read(3, "e; it is\npredisposed to if the g"..., 8192) = 8192
read(3, "ylate of soda in full doses, or\n"..., 8192) = 8192
read(3, "It may become purulent as a resu"..., 8192) = 8192
read(3, "uncle is a grave disease, especi"..., 8192) = 8192
read(3, " and\npacked.\n\n#Sporotrichosis# i"..., 8192) = 8192
read(3, "esulting wound being treated by "..., 8192) = 8192
read(3, " and the formation of\nfissures a"..., 8192) = 8192
read(3, "c ulcer with venous congestion o"..., 8192) = 8192
read(3, " the muscle is bruised at the sa"..., 8192) = 8192
read(3, " and prominent when the muscle i"..., 8192) = 8192
read(3, "m of paralysis resulting from\npo"..., 8192) = 8192
read(3, "mmation of its sheath, and give "..., 8192) = 8192
read(3, "rwise the affection\nwould be muc"..., 8192) = 8192
read(3, "eriod the lesion usually consist"..., 8192) = 8192
read(3, "irection\nof abduction and rotati"..., 8192) = 8192
read(3, "ls_ determines to some extent th"..., 8192) = 8192
read(3, "e of worm-eaten depressions and "..., 8192) = 8192
read(3, "ement of the limb, or it\nis the "..., 8192) = 8192
read(3, "form of moulded gutter splint is"..., 8192) = 8192
read(3, "p\" or with iodoform), which is\nc"..., 8192) = 8192
read(3, "he\ncentral clear area is either "..., 8192) = 8192
read(3, "e swelling often remains\nquiesce"..., 8192) = 8192
read(3, "rum in\nthe calcaneus.\n\n(Specimen"..., 8192) = 8192
read(3, "severe boring pain--as if a giml"..., 8192) = 8192
read(3, "formity and paraplegia. In the\np"..., 8192) = 8192
read(3, "covery.\n\n_Treatment._--The treat"..., 8192) = 8192
read(3, ", which was\nfirst described by R"..., 8192) = 8192
read(3, "trical enlargement and deformity"..., 8192) = 8192
read(3, "ty as by pressing\nupon a nerve-t"..., 8192) = 8192
read(3, " segment of bone is resected and"..., 8192) = 8192
read(3, "ignant forms\nis usually unavaili"..., 8192) = 8192
read(3, "iefly in chronic disease of\npyog"..., 8192) = 8192
read(3, "g._--A joint is said to lock whe"..., 8192) = 8192
read(3, "hout suppuration; there is again"..., 8192) = 8192
read(3, "(Harrison).\n\nMurphy has found be"..., 8192) = 8192
read(3, "hanges, resulting\nin the formati"..., 8192) = 8192
read(3, "ation of the peri-synovial\ncellu"..., 8192) = 8192
read(3, "-existence of indolent swellings"..., 8192) = 8192
read(3, "-intestinal\ntract, and those who"..., 8192) = 8192
read(3, " are exceptional.\n\n[Illustration"..., 8192) = 8192
read(3, "nation of these arthropathies th"..., 8192) = 8192
read(3, "rable time may elapse before the"..., 8192) = 8192
read(3, "ig. 167). The body is sometimes "..., 8192) = 8192
read(3, "s\n    abscess of, 448\n    aneury"..., 8192) = 8192
read(3, "Epidermis, grafting, 12\n    repa"..., 8192) = 8192
read(3, "changes in, in rickets, 470\n    "..., 8192) = 8192
read(3, "ciae, 416\n\n  Ossifying junction,"..., 8192) = 8192
read(3, ", 481\n\n  Suction bells, 39\n\n  Su"..., 8192) = 8192
read(3, " 55\n    subcutaneous, 56\n    sub"..., 8192) = 8192
read(3, "this electronic work, without\npr"..., 8192) = 8192
read(3, "nternal\nRevenue Service.  The Fo"..., 8192) = 8192
read(3, "d with animation and impulsivene"..., 8192) = 8192
read(3, "ur family's behalf that I'll sta"..., 8192) = 8192
read(3, "efly masculine, had formed\nround"..., 8192) = 8192
read(3, "nder observation, brought them i"..., 8192) = 8192
read(3, "the comedy of the people of Geno"..., 8192) = 8192
read(3, "y replaced by\nanother--a childli"..., 8192) = 8192
read(3, "\nIt was evident that Prince Andr"..., 8192) = 8192
read(3, "y entered the elegant, newly dec"..., 8192) = 8192
read(3, "s, and overshoes were lying abou"..., 8192) = 8192
read(3, "d\nhigher and higher and trembled"..., 8192) = 8192
read(3, "d, been up\nto heaven only knows "..., 8192) = 8192
read(3, "ble little feet\nwould carry her."..., 8192) = 8192
read(3, " paused and stood listening to t"..., 8192) = 8192
read(3, "dear,\" said the countess, \"my li"..., 8192) = 8192
read(3, "f.\"\n\nBoris bowed again politely."..., 8192) = 8192
read(3, "lt and\nhandsome young officer en"..., 8192) = 8192
read(3, "istcoat.\n\n\"Yes, immediately, imm"..., 8192) = 8192
read(3, "ss look toward the door, and now"..., 8192) = 8192
read(3, "e our\nturn next.\"\n\nThe colonel w"..., 8192) = 8192
read(3, "pt, sitting on the\nblue-striped "..., 8192) = 8192
read(3, "nd the agility with which he cap"..., 8192) = 8192
read(3, "you see, my dear princess and co"..., 8192) = 8192
read(3, "ooner?\"\n\n\"It's in the inlaid por"..., 8192) = 8192
read(3, "p, and\nsat down in the lady's ch"..., 8192) = 8192
read(3, " momentary glimpse between their"..., 8192) = 8192
read(3, "s.\n\n\"Pierre, my dear, come here."..., 8192) = 8192
read(3, "egular hum of a lathe. The princ"..., 8192) = 8192
read(3, " I know very well that Count Nic"..., 8192) = 8192
read(3, "tmosphere, careless,\nlighthearte"..., 8192) = 8192
read(3, " cheek.\n\nThe old man was in a go"..., 8192) = 8192
read(3, " not at all know when \"you and I"..., 8192) = 8192
read(3, "demoiselle Bourienne at all,\" sa"..., 8192) = 8192
read(3, " windows. The\ndomestic serfs wer"..., 8192) = 8192
read(3, "e\ncommander in chief to look und"..., 8192) = 8192
read(3, "saddle,\nrighted himself, drew hi"..., 8192) = 8192
read(3, "?\" asked the regimental commande"..., 8192) = 8192
read(3, " And believe me on my honour tha"..., 8192) = 8192
read(3, "et the enemy.\n\nPrince Andrew was"..., 8192) = 8192
read(3, "he's\nlost and will come back in "..., 8192) = 8192
read(3, "nd rushing at the man\nwith a thr"..., 8192) = 8192
read(3, "r\nfellow? You landed yourself in"..., 8192) = 8192
read(3, "he saddle.\n\n\"I'll really call in"..., 8192) = 8192
read(3, "twitched its ears as the bayonet"..., 8192) = 8192
read(3, "only knows what they're about!\" "..., 8192) = 8192
read(3, " air that made it impossible to "..., 8192) = 8192
read(3, "rian court, now no\nlonger at Vie"..., 8192) = 8192
read(3, "rived\nof all the comforts of cle"..., 8192) = 8192
read(3, "ken and I hope it will not be, f"..., 8192) = 8192
read(3, " of them, but as far as I the fa"..., 8192) = 8192
read(3, "hand. The sergeant,\nwho was evid"..., 8192) = 8192
read(3, " officer was in that state of se"..., 8192) = 8192
read(3, " the Bohemian mountains, defendi"..., 8192) = 8192
read(3, "you\nsomething.\"\n\nThey dismounted"..., 8192) = 8192
read(3, "ten the French.\n\n\"We have orders"..., 8192) = 8192
read(3, "dka a quarter of an hour before,"..., 8192) = 8192
read(3, "nemy's attack very long. About T"..., 8192) = 8192
read(3, "eft... left!\" \"Close up!\" came t"..., 8192) = 8192
read(3, " and hussars alike, felt that th"..., 8192) = 8192
read(3, "e had maintained its order in th"..., 8192) = 8192
read(3, "ng in a gasping\nvoice:\n\n\"Are you"..., 8192) = 8192
read(3, "ck!\"\n\nWith the soldier, an infan"..., 8192) = 8192
read(3, "hey, these soldiers-\nwounded and"..., 8192) = 8192
read(3, "d it is\npurely out of charity th"..., 8192) = 8192
read(3, " Pierre, was so close to him tha"..., 8192) = 8192
read(3, "arguments as nonsense in compari"..., 8192) = 8192
read(3, "red by this general admiration h"..., 8192) = 8192
read(3, "y.\n\nOld Bolkonski had always had"..., 8192) = 8192
read(3, "ends on this.\"\n\nIn the meantime,"..., 8192) = 8192
read(3, "g thought (but yet with a hope f"..., 8192) = 8192
read(3, "t him with curiosity and\nperfect"..., 8192) = 8192
read(3, "ooked straight into his handsome"..., 8192) = 8192
read(3, "outed when the princess, as if l"..., 8192) = 8192
read(3, "fficer; he is well now, he\nwrote"..., 8192) = 8192
read(3, "e Constantine Pavlovich, who com"..., 8192) = 8192
read(3, "uttural voice.\n\n\"Go across to ou"..., 8192) = 8192
read(3, "d the room, Prince Andrew turned"..., 8192) = 8192
read(3, "gh not loud voice, attracted eve"..., 8192) = 8192
read(3, ", subordination, which made\nthis"..., 8192) = 8192
read(3, " I should be\nvery glad to do all"..., 8192) = 8192
read(3, "French had been defeated and wer"..., 8192) = 8192
read(3, " the hand on the dial of human h"..., 8192) = 8192
read(3, " a loud, monotonous voice began "..., 8192) = 8192
read(3, " to Weyrother, and to the\nEmpero"..., 8192) = 8192
read(3, "white horse. Prince Bagration an"..., 8192) = 8192
read(3, "of an army,\nannouncing the appro"..., 8192) = 8192
read(3, "d his cold face wore that specia"..., 8192) = 8192
read(3, "the faces of his suite, young an"..., 8192) = 8192
read(3, "it was difficult to get out agai"..., 8192) = 8192
read(3, " understand\nor make out anything"..., 8192) = 8192
read(3, "y horse to get quickly past thes"..., 8192) = 8192
read(3, "oss the ditch on foot. The Emper"..., 8192) = 8192
read(3, "e of them and at\nonce forgot the"..., 8192) = 8192
read(3, "e. Don't you see? That's\nour hou"..., 8192) = 8192
read(3, " but Natasha, taking\nher brother"..., 8192) = 8192
read(3, "ing men was training a trotter o"..., 8192) = 8192
read(3, "ough all were in a conspiracy of"..., 8192) = 8192
read(3, "h his hands; he was more\naccusto"..., 8192) = 8192
read(3, "ery handsome,\" thought Pierre, \""..., 8192) = 8192
read(3, "d forty paces apart at\nthe farth"..., 8192) = 8192
read(3, "y, and she\nreplied with a calm s"..., 8192) = 8192
read(3, "egret of myself and of the\nwhole"..., 8192) = 8192
read(3, "ince Andrew's study into the bed"..., 8192) = 8192
read(3, "hands and remained so for some m"..., 8192) = 8192
read(3, "nisov...\"\n\n\"Oh, Denisov is quite"..., 8192) = 8192
read(3, "gel, flying about like a\nfeather"..., 8192) = 8192
read(3, "y?\" Rostov asked himself.\n\n\"Well"..., 8192) = 8192
read(3, " him most.\n\n\"It's all up! I'm lo"..., 8192) = 8192
read(3, " widely\nopened eyes. \"What has h"..., 8192) = 8192
read(3, "er there were no horses or the p"..., 8192) = 8192
read(3, "\nto you. Where are you traveling"..., 8192) = 8192
read(3, "into an\nabyss of deceit and mise"..., 8192) = 8192
read(3, "n\nan entirely new life quite unl"..., 8192) = 8192
read(3, "been done, the Rhetor said:\n\n\"In"..., 8192) = 8192
read(3, "o other distinctions,\" read the\n"..., 8192) = 8192
read(3, "adt and the\nsurrender of most of"..., 8192) = 8192
read(3, "tty but unjust,\" said Anna Pavlo"..., 8192) = 8192
read(3, "ed Princess Mary\nwith a sigh.\n\nP"..., 8192) = 8192
read(3, "the battle of Pultusk, which is "..., 8192) = 8192
read(3, "nd he did not know how the rest,"..., 8192) = 8192
read(3, "orests of fir and birch, which w"..., 8192) = 8192
read(3, ". I lived for glory.--And after "..., 8192) = 8192
read(3, "hey grow more\nirritable, become "..., 8192) = 8192
read(3, "fe.\n\n\n\n\n\nCHAPTER XIII\n\n\nIt was g"..., 8192) = 8192
read(3, "at her and now at Pierre, that s"..., 8192) = 8192
read(3, " the\ndebt to his parents.\n\nOur a"..., 8192) = 8192
read(3, "m he\nwas speaking to, and saw th"..., 8192) = 8192
read(3, "er to the next in seniority and "..., 8192) = 8192
read(3, "e assistant led Rostov to the\nof"..., 8192) = 8192
read(3, "im and accepted him. Twice he ha"..., 8192) = 8192
read(3, "tov. \"If only I were to\nhand the"..., 8192) = 8192
read(3, "to\nhim and, bending his head, sm"..., 8192) = 8192
read(3, " called, was such that when Napo"..., 8192) = 8192
read(3, " and by the more important of th"..., 8192) = 8192
read(3, " Princess Mary entering at such "..., 8192) = 8192
read(3, "vague idea, and Speranski its ch"..., 8192) = 8192
read(3, "bition maybe,\" Speranski put in "..., 8192) = 8192
read(3, ", but we have\nno laws. That is w"..., 8192) = 8192
big.txt.3                                   65%[========================================================>                              ]   4.06M  10.0MB/s               read(3, "ything was plunged in darkness, "..., 8192) = 8192
read(3, "nversations with him, and\ndeduce"..., 8192) = 8192
read(3, "t was Boris Drubetskoy who was\na"..., 8192) = 8192
read(3, "dy, flying up to the clouds. And"..., 8192) = 8192
read(3, " note of hand for only sixty tho"..., 8192) = 8192
read(3, " about? You have quite turned hi"..., 8192) = 8192
read(3, "d Natasha\nturning her head and c"..., 8192) = 8192
read(3, "king, are even\nmore run after.\"\n"..., 8192) = 8192
read(3, "Pierre indicated.\nThe despairing"..., 8192) = 8192
read(3, "counts\npublished,\" recounted Bit"..., 8192) = 8192
read(3, "had translated the articles of\nt"..., 8192) = 8192
read(3, "ife glanced\nat one another, both"..., 8192) = 8192
read(3, "ha as he rose.\n\n\"I must... I mus"..., 8192) = 8192
read(3, "ther consent to this\nmarriage an"..., 8192) = 8192
read(3, "sed her lips\nto his forehead as "..., 8192) = 8192
read(3, " for\nleaving them alone, even wh"..., 8192) = 8192
read(3, " father's remarks and his talks\n"..., 8192) = 8192
read(3, " about this for a long time, and"..., 8192) = 8192
read(3, "ather and mother were much the s"..., 8192) = 8192
read(3, " it was decided to give\nthem a t"..., 8192) = 8192
read(3, "nd pulled at his horse. Natasha "..., 8192) = 8192
read(3, ". The hazel bushes parted behind"..., 8192) = 8192
read(3, "u!\" shouting and preparing to\ndi"..., 8192) = 8192
read(3, "t one another's dogs, trying\nnot"..., 8192) = 8192
read(3, "horse's\nback as if by that gestu"..., 8192) = 8192
read(3, "y refused\npublic appointments, p"..., 8192) = 8192
read(3, "r with quite a new tenderness.\nH"..., 8192) = 8192
read(3, "and higher than all else.\"\n\nNich"..., 8192) = 8192
read(3, "e\nwhen she was with him and he w"..., 8192) = 8192
read(3, "to play, blundered in\nhis talk w"..., 8192) = 8192
read(3, "g new I've never seen before. Th"..., 8192) = 8192
read(3, " Sonya was brighter, more animat"..., 8192) = 8192
read(3, "urged Natasha, seizing her hand."..., 8192) = 8192
read(3, "rew and Natasha and\nthe death of"..., 8192) = 8192
read(3, " to be, whatever he engaged in, "..., 8192) = 8192
read(3, "nne--had evidently pleased him, "..., 8192) = 8192
read(3, "orted to him. The\ntone of the co"..., 8192) = 8192
read(3, "Mary.\n\n\"To please Moscow girls n"..., 8192) = 8192
read(3, "en visited the Karagins, while p"..., 8192) = 8192
read(3, " French greeting her\nslightly co"..., 8192) = 8192
read(3, " thought very plain, affected, a"..., 8192) = 8192
read(3, "er with a happy look on\nher face"..., 8192) = 8192
read(3, "Natasha tried to overhear it jus"..., 8192) = 8192
read(3, "sed her, yet his presence made h"..., 8192) = 8192
read(3, "of it too and did\nnot refuse him"..., 8192) = 8192
read(3, "dined with me yesterday--we near"..., 8192) = 8192
read(3, " and talk, dressmakers came agai"..., 8192) = 8192
read(3, "\n\nNatasha looked at Sonya with w"..., 8192) = 8192
read(3, "lanation?\nBut what is there to o"..., 8192) = 8192
read(3, "gentlemen\" he always drove himse"..., 8192) = 8192
read(3, "e wicket gate and\nwas struggling"..., 8192) = 8192
read(3, "led to him.\n\n\"Pierre! Been back "..., 8192) = 8192
read(3, " dined that day with\nDolokhov, c"..., 8192) = 8192
read(3, "the\nstudy. Prince Andrew, greatl"..., 8192) = 8192
read(3, "up in Pierre. He felt the tears "..., 8192) = 8192
read(3, "uses nothing\ncould have happened"..., 8192) = 8192
read(3, "am out of\nthe vast forest that h"..., 8192) = 8192
read(3, "ward the expenses. Boris\nwas now"..., 8192) = 8192
read(3, "rench\nhussars looked silently at"..., 8192) = 8192
read(3, "hev was brought in.\nHe became st"..., 8192) = 8192
read(3, "re he stood he began\nspeaking in"..., 8192) = 8192
read(3, "\" said Napoleon. \"I have\nallies-"..., 8192) = 8192
read(3, "r taken command of the armies? W"..., 8192) = 8192
read(3, "nly because of\nPrincess Mary: th"..., 8192) = 8192
read(3, "ew rode round the whole fortifie"..., 8192) = 8192
read(3, "uccessfully without unity of\ncom"..., 8192) = 8192
read(3, "ing bedstead of Bennigsen's adju"..., 8192) = 8192
read(3, " table, and put\nquestions on whi"..., 8192) = 8192
read(3, "ed men. Bagration was the best, "..., 8192) = 8192
read(3, "as was his habit, and\ncrowded Ro"..., 8192) = 8192
read(3, "e pretexts. When\nhe had gone, ta"..., 8192) = 8192
read(3, " beside him, was gazing\nlike him"..., 8192) = 8192
read(3, "e all, that thought was kept out"..., 8192) = 8192
read(3, "ot because Pierre was\na married "..., 8192) = 8192
read(3, "so elevating and soothing an eff"..., 8192) = 8192
read(3, "to be fixed in the sky and felt "..., 8192) = 8192
read(3, "ys.\n\nPierre came early so as to "..., 8192) = 8192
read(3, "with a quiver in her voice due c"..., 8192) = 8192
read(3, "r cheeks.\n\n\"Father! Angel! Dear "..., 8192) = 8192
read(3, "nobles, whom Pierre met every da"..., 8192) = 8192
read(3, "the tombs of our fathers, to car"..., 8192) = 8192
big.txt.3                                   75%[================================================================>                      ]   4.69M  7.75MB/s               read(3, "esence of Kurakin and then of Ba"..., 8192) = 8192
read(3, "d not have refrained from\nhad th"..., 8192) = 8192
read(3, "hem a plan he had\ndrawn and fore"..., 8192) = 8192
read(3, "ne so before he felt the bed roc"..., 8192) = 8192
read(3, "ngle. 'One\nman though undone is "..., 8192) = 8192
read(3, "ermittent whistle of a shell, fl"..., 8192) = 8192
read(3, "f the\ncollapsing roof of the bar"..., 8192) = 8192
read(3, "you can and tell the serfs to go"..., 8192) = 8192
read(3, "hat life of the salons is unchan"..., 8192) = 8192
read(3, " you know what he said to\nthe Em"..., 8192) = 8192
read(3, "d wrote a letter to the\ncommande"..., 8192) = 8192
read(3, "e.\n\n\"But what could have happene"..., 8192) = 8192
read(3, "They all drew back from the bed,"..., 8192) = 8192
read(3, "uneral and told him to have\ntwel"..., 8192) = 8192
read(3, " move\nnow. If we go we are almos"..., 8192) = 8192
read(3, "em to go away,\"\nsaid Princess Ma"..., 8192) = 8192
read(3, "w his face before her. And not t"..., 8192) = 8192
read(3, ". She turned away,\nand then, as "..., 8192) = 8192
read(3, "to Princess Mary's\nexpressions o"..., 8192) = 8192
read(3, "!\" shouted a Cossack standing at"..., 8192) = 8192
read(3, "old age and experience of life. "..., 8192) = 8192
read(3, "influences, the popular choice o"..., 8192) = 8192
read(3, "s knight at all, and\nhave not be"..., 8192) = 8192
read(3, "ngry with himself for\ndoing so. "..., 8192) = 8192
read(3, "\nmilitia, and for many other rea"..., 8192) = 8192
read(3, " stopped by the side of the road"..., 8192) = 8192
read(3, ", the smoke of campfires, villag"..., 8192) = 8192
read(3, " one another, crowding, panting,"..., 8192) = 8192
read(3, "here\nmilitiamen were digging. Th"..., 8192) = 8192
read(3, "he regimental\npaymaster.\n\nPrince"..., 8192) = 8192
read(3, " whom you have\nridden round the "..., 8192) = 8192
read(3, "agination. On one of them he dwe"..., 8192) = 8192
read(3, "ausset and the officer on duty. "..., 8192) = 8192
read(3, "ice-King made to execute the ord"..., 8192) = 8192
read(3, " am the\nmore tranquil and confid"..., 8192) = 8192
read(3, "and\nmagically colored and outlin"..., 8192) = 8192
read(3, " me,\" said Pierre. \"I'll go up o"..., 8192) = 8192
read(3, " the trench and was particularly"..., 8192) = 8192
read(3, "that space there was, on the one"..., 8192) = 8192
read(3, "Russian wing?\"\n\n\"Tell the King o"..., 8192) = 8192
read(3, " him. They were Russians.\n\nThe R"..., 8192) = 8192
read(3, "rench:\n\n\"Then you do not think, "..., 8192) = 8192
read(3, "thought Prince Andrew, looking w"..., 8192) = 8192
read(3, "during, Prince Andrew enjoyed a\n"..., 8192) = 8192
read(3, "nt antinational. I should have a"..., 8192) = 8192
read(3, "ong the old\nSmolensk road, the d"..., 8192) = 8192
read(3, " of a falling body increases as "..., 8192) = 8192
read(3, "generally. Though they had not b"..., 8192) = 8192
read(3, "tal without a struggle, or are w"..., 8192) = 8192
read(3, "army to fight, and that if it co"..., 8192) = 8192
read(3, " to human desires. And with\nthis"..., 8192) = 8192
read(3, "alousy of her daughter, and now "..., 8192) = 8192
read(3, "ng is fear, and how shamefully I"..., 8192) = 8192
read(3, "fight in the streets. Do not be "..., 8192) = 8192
read(3, "fallen into the clutches\nof the "..., 8192) = 8192
read(3, "saw an intervention of Providenc"..., 8192) = 8192
read(3, "aid the count with involuntary v"..., 8192) = 8192
read(3, "ouse serfs, and peasants, with\nw"..., 8192) = 8192
read(3, "h a spirit of heroism and the\nle"..., 8192) = 8192
read(3, "f them?\"\n\n\"Let them have my ward"..., 8192) = 8192
read(3, "at, with a\nqueer-looking old boy"..., 8192) = 8192
read(3, "rousing himself and rising hurri"..., 8192) = 8192
read(3, "he\nhad to put on a burnoose and "..., 8192) = 8192
read(3, "ing through Moscow from two o'cl"..., 8192) = 8192
read(3, " at vespertime,\"\nsaid Mavra Kuzm"..., 8192) = 8192
read(3, "n ukase!\" cried\nvoices in the cr"..., 8192) = 8192
read(3, " wishes. If\nthe government offic"..., 8192) = 8192
read(3, "ou'll see! And you said the\nFren"..., 8192) = 8192
read(3, "y... Where are you going?... Thi"..., 8192) = 8192
read(3, "ion in which they had found the\n"..., 8192) = 8192
read(3, "here valuables were to be had fo"..., 8192) = 8192
read(3, ": the feeling which causes a man"..., 8192) = 8192
read(3, "stened in silence\nwith the same "..., 8192) = 8192
read(3, "s--the capital of the world,\" Pi"..., 8192) = 8192
read(3, "is childhood, youth, and\nmanhood"..., 8192) = 8192
read(3, "rs, after attending to\ntheir mas"..., 8192) = 8192
read(3, " fire at Little\nMytishchi a mile"..., 8192) = 8192
read(3, "s thoughts, and when he came to "..., 8192) = 8192
read(3, "at's this?\" said the doctor, ris"..., 8192) = 8192
read(3, "Was it for this I nursed you...."..., 8192) = 8192
read(3, "r like some\nlittle wild animal. "..., 8192) = 8192
read(3, "f, besides several looters. But "..., 8192) = 8192
big.txt.3                                   86%[=========================================================================>             ]   5.33M  6.62MB/s               read(3, "fulfill the desires of Your Maje"..., 8192) = 8192
read(3, "t myself in\nall things to His wi"..., 8192) = 8192
read(3, "houses with\nstationmasters aslee"..., 8192) = 8192
read(3, "em!\" said Nicholas.\n\n\"Her niece,"..., 8192) = 8192
read(3, "atchmaking would be an insult to"..., 8192) = 8192
read(3, "e, beyond\nthe choir.\n\nNicholas i"..., 8192) = 8192
read(3, "rney to\nYaroslavl, and a few day"..., 8192) = 8192
read(3, "ll that had occurred\nthat day, e"..., 8192) = 8192
read(3, "back to him in his\ndesignation a"..., 8192) = 8192
read(3, "h a\nfirm regular tread and halte"..., 8192) = 8192
read(3, "on the word\nwho.\n\nSitting silent"..., 8192) = 8192
read(3, "ola and Lavra! Lord Jesus\nChrist"..., 8192) = 8192
read(3, "off. That she had not heard from"..., 8192) = 8192
read(3, "lder.\n\nAs soon as Natasha, sitti"..., 8192) = 8192
read(3, "t her glance, did not look at he"..., 8192) = 8192
read(3, "you come in.\nNo one else gives m"..., 8192) = 8192
read(3, "l events this way or that; yet t"..., 8192) = 8192
read(3, " of that wounded beast (the Fren"..., 8192) = 8192
read(3, "or. I need only advise anything "..., 8192) = 8192
read(3, "utuzov had to consent.\n\n\n\n\n\nCHAP"..., 8192) = 8192
read(3, " said he to\nMiloradovich who ask"..., 8192) = 8192
read(3, "e our shame. We have paid for th"..., 8192) = 8192
read(3, "ef be distributed to those who h"..., 8192) = 8192
read(3, "out him. This little dog lived i"..., 8192) = 8192
read(3, "t away. The Frenchman looked at "..., 8192) = 8192
read(3, " Oh, it will be the death of me!"..., 8192) = 8192
read(3, "!... Oh, the rascals!... See how"..., 8192) = 8192
read(3, "ing\nseparated from the rest of t"..., 8192) = 8192
read(3, "ardly had he done so before he l"..., 8192) = 8192
read(3, "ave a detailed account from the "..., 8192) = 8192
read(3, "fight, why block the\nroad, losin"..., 8192) = 8192
read(3, "lways occurs in wars that\ntake o"..., 8192) = 8192
read(3, "nisov's vicinity),\nthe commander"..., 8192) = 8192
read(3, "n through the dispatch.\n\"Why did"..., 8192) = 8192
read(3, "sov then relieved him\nfrom drudg"..., 8192) = 8192
read(3, "r the fire of the French and had"..., 8192) = 8192
read(3, "s they are and their\nnumbers,\" s"..., 8192) = 8192
read(3, "ht every moment as he\nstood by t"..., 8192) = 8192
read(3, "harmonious orchestra playing\nsom"..., 8192) = 8192
read(3, "ent to the gate to meet\nDenisov "..., 8192) = 8192
read(3, "asionally he glanced at the fami"..., 8192) = 8192
read(3, " was now an expression of quiet "..., 8192) = 8192
read(3, "o diverge from the truth in\ndesc"..., 8192) = 8192
read(3, "al running away, described in\nor"..., 8192) = 8192
read(3, "the Berezina. It is only\npossibl"..., 8192) = 8192
read(3, "o.\n\nShe felt all the time as if "..., 8192) = 8192
read(3, "using\nto believe that she could "..., 8192) = 8192
read(3, "enemy hands while the sick Russi"..., 8192) = 8192
read(3, " up to Kutuzov with personal rep"..., 8192) = 8192
read(3, " several of them.\n\n\"I thank you "..., 8192) = 8192
read(3, "e out of their shirts.\n\n\n\n\n\nCHAP"..., 8192) = 8192
read(3, "es heavy with hoarfrost.\n\nAbout "..., 8192) = 8192
read(3, "ich (in consequence of\nthe Peter"..., 8192) = 8192
read(3, "his face now.\n\nWhen Kutuzov came"..., 8192) = 8192
read(3, "y absence of an aim gave him the"..., 8192) = 8192
read(3, "his family\naffairs, his wife's a"..., 8192) = 8192
read(3, "raftsmanship, with luxury, and g"..., 8192) = 8192
read(3, "ich he himself had\nbeen unaware."..., 8192) = 8192
read(3, "asy to be an interesting man (I "..., 8192) = 8192
read(3, "le to forget?\" said she.\n\n\"It di"..., 8192) = 8192
read(3, "f. With a deep and\nlong-drawn si"..., 8192) = 8192
read(3, "Everything: her face, walk, look"..., 8192) = 8192
read(3, "of power in Europe, or a certain"..., 8192) = 8192
read(3, "nders without a shot; his most r"..., 8192) = 8192
read(3, "ance alone, without\nany conspira"..., 8192) = 8192
read(3, "ng them.\n\nNot one of the plans N"..., 8192) = 8192
read(3, " spoke of the countess' health, "..., 8192) = 8192
read(3, "ome failure or irregularity, he\n"..., 8192) = 8192
read(3, "Sonya, blaming himself and comme"..., 8192) = 8192
read(3, " think he is asleep--he was so t"..., 8192) = 8192
read(3, " of her children, and sharing ev"..., 8192) = 8192
read(3, "aby herself, a thing then unhear"..., 8192) = 8192
read(3, "ns to the\nothers. Every event, j"..., 8192) = 8192
read(3, "\"Oh yes, they've come,\" she woul"..., 8192) = 8192
read(3, "earer I got to the house the mor"..., 8192) = 8192
read(3, "can only cause harm.\"\n\n\"Why? Did"..., 8192) = 8192
read(3, "at it is\nevery honest man's duty"..., 8192) = 8192
read(3, "ite lost the knack of talking to"..., 8192) = 8192
read(3, ".\n\n\"No,\" answered Nicholas, and "..., 8192) = 8192
read(3, "gain ordering\ntheir armies to ki"..., 8192) = 8192
read(3, "following the path laid down by "..., 8192) = 8192
read(3, ", decide to\nsubstitute for it mo"..., 8192) = 8192
read(3, " power is entrusted\nconsist in t"..., 8192) = 8192
read(3, " front leads\nit and the collecti"..., 8192) = 8192
read(3, "the whole series of commands is "..., 8192) = 8192
read(3, "atever direction a ship moves, t"..., 8192) = 8192
read(3, " may show a man that under the\ns"..., 8192) = 8192
read(3, "freedom to inevitability decreas"..., 8192) = 8192
read(3, "e correctly we connect the effec"..., 8192) = 8192
read(3, " give it\nvisibility is infinite,"..., 8192) = 8192
read(3, " conception of the movements of "..., 8192) = 8192
read(3, "t of Columbia, Florida, Georgia,"..., 8192) = 8192
big.txt.3                                   97%[====================================================================================>  ]   6.05M  6.01MB/s               read(3, "ment provisions of this\n     \"Sm"..., 8192) = 8192
read(3, "ion\n specific\n customer\n box\n ou"..., 8192) = 8192
read(3, "ef\n gate\n elderly\n persuade\n ove"..., 8192) = 8192
read(3, "ed\n mouse\n strain\n specialist\n c"..., 8192) = 8192
read(3, "irt\n coordinate\n tactic\n influen"..., 8192) = 8192
read(3, "sterious\n dancer\n trail\n caution"..., 8192) = 8192
read(3, "red\n bomber\n voltage\n unusually\n"..., 8192) = 8192
read(3, "  years  off  face  nothing  rig"..., 8192) = 8192
read(3, "erwise  stream  honor  carefully"..., 8192) = 8192
read(3, "t  cap  waves  woman's  glanced "..., 8192) = 8192
read(3, "ponsibility  beating  disappoint"..., 8192) = 8192
read(3, "her  insult  den  representation"..., 8192) = 8192
read(3, " excellence  transfer  awaited  "..., 8192) = 8192
read(3, "per  basin  creeping  matthew  p"..., 8192) = 8192
read(3, "rice  corrected  mystic  infancy"..., 8192) = 8192
read(3, "n  thereto  heaped  jewel  regai"..., 8192) = 8192
read(3, "ds  lilies  ballad  befall  cyli"..., 8192) = 8192
read(3, "t  basic  mutually  chris  greed"..., 6448) = 6448
big.txt.3                                  100%[======================================================================================>]   6.19M  5.93MB/s    in 1.0s

2020-01-31 05:16:57 (5.93 MB/s) - big.txt.3 saved [6488666/6488666]

/: Scheme missing.
FINISHED --2020-01-31 05:16:57--
Total wall clock time: 1.1s
Downloaded: 1 files, 6.2M in 1.0s (5.93 MB/s)
+++ exited with 1 +++

[q8 (2pts)] Do you conclude that wget typically fills its buffer or not?
The wget for this file does not fill its buffer.

[q9 (2pts)] Why do you think results are different than in the previous experiment?
I believe the results are different because of how wget is accessing the files. The first file is accessed remotely, and the second is on site.
