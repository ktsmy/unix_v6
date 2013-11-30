Unix v6 (PDP-11) on SIMH
========================

See http://gunkies.org/wiki/Installing_Unix_v6_%28PDP-11%29_on_SIMH

# Setup SIMH
See http://simh.trailing-edge.com/

    $ wget http://simh.trailing-edge.com/sources/simhv39-0.zip
    $ unzip simhv39-0.zip -d simh
    $ cd simh
    $ make all
    $ cd ..

./simh/BIN/pdp11 (and all other executables) is created.

# get Unix v6 tape
From http://sourceforge.net/projects/bsd42/files/Install%20tapes/Research%20Unix/

    $ wget http://jaist.dl.sourceforge.net/project/bsd42/Install%20tapes/Research%20Unix/Unix-v6-Ken-Wellsch.tap.bz2
    $ bunzip2 Unix-v6-Ken-Wellsch.tap.bz2
    $ mv Unix-v6-Ken-Wellsch.tap dist.tap

# Install Unix v6 on SIMH
1. Tape boot
Boot with tboot.ini

    $ ./simh/BIN/pdp11 tboot.ini

Output example:

    PDP-11 simulator V3.9-0
    Disabling XQ
    RK: creating new file
    RK: creating new file
    RK: creating new file
[hit Ctrl+E]
    Simulation stopped, PC: 100012 (BR 100012)
    sim> g 0
    =tmrk
    disk offset
    0
    tape offset
    100
    count
    1
    =tmrk
    disk offset
    1
    tape offset
    101
    count
    3999
    = [hit Ctrl+E]
    Simulation stopped, PC: 137274 (TSTB @#177560)
    sim> q
    Goodbye

2. Disk install
Boot with tboot.ini

    $ ./simh/BIN/pdp11 dboot.ini

Output example:

    PDP-11 simulator V3.9-0
    Disabling XQ
    @rkunix
    mem = 1035
    RESTRICTED RIGHTS
    
    Use, duplication or disclosure is subject to
    restrictions stated in Contract with Western
    Electric Company, Inc.
    # STTY -LCASE
    # chdir /usr/sys/conf
    # cc mkconf.c
    # mv a.out mkconf
    # ./mkconf
    rk
    tm
    tc
    8dc
    lp
    done
    # as m40.s
    # mv a.out m40.o
    # cc -c c.c
    # as l.s
    # ld -x a.out m40.o c.o ../lib1 ../lib2
    # mv a.out /unix
    # ls -l /unix
    -rwxrwxrwx  1 root    30346 Oct 10 12:33 /unix
    # /etc/mknod /dev/rk0 b 0 0
    # /etc/mknod /dev/rk1 b 0 1
    # /etc/mknod /dev/rk2 b 0 2
    # /etc/mknod /dev/mt0 b 3 0
    # /etc/mknod /dev/tap0 b 4 0
    # /etc/mknod /dev/rrk0 c 9 0
    # /etc/mknod /dev/rrk1 c 9 1
    # /etc/mknod /dev/rrk2 c 9 2
    # /etc/mknod /dev/rmt0 c 12 0
    # /etc/mknod /dev/lp0 c 2 0
    # /etc/mknod /dev/tty0 c 3 0
    # /etc/mknod /dev/tty1 c 3 1
    # /etc/mknod /dev/tty2 c 3 2
    # /etc/mknod /dev/tty3 c 3 3
    # /etc/mknod /dev/tty4 c 3 4
    # /etc/mknod /dev/tty5 c 3 5
    # /etc/mknod /dev/tty6 c 3 6
    # /etc/mknod /dev/tty7 c 3 7
    # chmod 640 /dev/*rk*
    # chmod 640 /dev/*mt*
    # chmod 640 /dev/*tap*
    # dd if=/dev/mt0 of=/dev/rk1 count=4000 skip=4100
    4000+0 records in
    4000+0 records out
    # dd if=/dev/mt0 of=/dev/rk2 count=4000 skip=8100
    4000+0 records in
    4000+0 records out
    # mkdir /usr/doc
    # /etc/mount /dev/rk1 /usr/source
    # /etc/mount /dev/rk2 /usr/doc
    # cat >> /etc/rc
    /etc/mount /dev/rk1 /usr/source
    /etc/mount /dev/rk2 /usr/doc
    [hit Ctrl+D]
    # chdir /usr/source/s1
    # ed df.c
    1282
    /rp0/d
    .-2a
    "/dev/rk0",
    "/dev/rk1",
    .
    w
    1293
    q
    # cc df.c
    # cp a.out > /bin/df
    # icheck /dev/rrk0
    /dev/rrk0:
    spcl      22
    files    294
    large     96
    direc     25
    indir     96
    used    2918
    free     995
    # dcheck /dev/rrk0
    /dev/rrk0:
    # icheck /dev/rrk1
    /dev/rrk1:
    spcl       0
    files    596
    large     98
    direc     34
    indir     98
    used    2978
    free     935
    # dcheck /dev/rrk1
    /dev/rrk1:
    # icheck /dev/rrk2
    /dev/rrk2:
    bad freeblock
    missing 1499
    spcl       0
    files    337
    large     69
    direc     25
    indir     69
    used    2222
    free     192
    # dcheck /dev/rrk2
    /dev/rrk2:
    # ed /etc/ttys
    112
    1,8s/^0/1/p
    170
    w
    112
    q
    # sync
    # sync
    # sync
    # sync
    # [hit Ctrl+E]
    Simulation stopped, PC: 002502 (MOV (SP)+,177776)
    sim> q
    Goodbye
    
3. After disk install, you can boot normally with boot.ini
    
    $ ./simh/BIN/pdp11 boot.ini

Then, Unix v6 will boot as following:

    PDP-11 simulator V3.9-0
    Disabling XQ
    LPT: creating new file
    Listening on port 5555 (socket 8)
    @unix
    
    login: root
    #
 
