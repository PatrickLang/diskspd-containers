# diskspd-containers

These are a few containers I built for doing some simple disk benchmarks on different VM configurations running containers.

They are publically available on Docker Hub

- `patricklang/diskspd:ltsc2019` - for testing on Windows 10 (1809 or later) or Windows Server 2019.

> TODO: Linux container

Windows Source: https://github.com/Microsoft/diskspd
Windows Binaries: https://aka.ms/diskspd 

> TODO: Linux Source: 
> TODO: Linux Binaries: 

## Running Diskspd

If you `docker run` these containers, the default commandline is `diskspd -c1G -d120 -r -w50 -t8 -o8 -b4K -L testfile.dat`.

- `-c1G`: Create a 1 Gigabyte file for testing called `testfile.dat`
- `-d120`: Test for 120 seconds
- `-r`: Align IO to block size
- `-w50`: 50% write, 50% read
- `-t8`: 8 threads per CPU
- `-o8`: 8 async IO per thread
- `-b4K`: 4k IO blocks
- `-Sh`: disable OS & hardware write caching
- `-L`: track latency statistics

Caching is enabled

That translates to testing 4k random IO 4k aligned in a 1 gigabyte file, 50% read 50% write, with 8 threads per CPU core, 8 async IOs per thread for a duration of 120 seconds.


### Windows




### Example Results

Here's a few example results from testing this out. To accurately benchmark these should be run multiple times on unloaded systems to make sure they're consistent. 

#### Desktop with Intel Core i7-6700, Micron M600 256GB SATA SSD
Windows 10 version 1903, Docker for Windows 2.0.3.0, lots of stuff including VMs running in the background. It's importatnt to note that running Windows Server 2019 containers uses Hyper-V, which by default with Docker will only use 2 cores. This may be improved by adding more cores to the container config.

```
docker run -it diskspd:ltsc2019

Command Line: diskspd.exe -c1G -d120 -r -w50 -t8 -o8 -b4K -Sh -L testfile.dat

Input parameters:

        timespan:   1
        -------------
        duration: 120s
        warm up time: 5s
        cool down time: 0s
        measuring latency
        random seed: 0
        path: 'testfile.dat'
                think time: 0ms
                burst size: 0
                software cache disabled
                hardware write cache disabled, writethrough on
                performing mix test (read/write ratio: 50/50)
                block size: 4096
                using random I/O (alignment: 4096)
                number of outstanding I/O operations: 8
                thread stride size: 0
                threads per file: 8
                using I/O Completion Ports
                IO priority: normal

System information:

        computer name: e1e8c95b2207
        start time: 2019/07/25 21:46:27 UTC

Results for timespan 1:
*******************************************************************************

actual test time:       120.00s
thread count:           8
proc count:             2

CPU |  Usage |  User  |  Kernel |  Idle
-------------------------------------------
   0|  63.59%|   4.09%|   59.51%|  36.41%
   1|  36.82%|   5.22%|   31.60%|  63.18%
-------------------------------------------
avg.|  50.21%|   4.65%|   45.55%|  49.79%

Total IO
thread |       bytes     |     I/Os     |    MiB/s   |  I/O per s |  AvgLat  | LatStdDev |  file
-----------------------------------------------------------------------------------------------------
     0 |      2910072832 |       710467 |      23.13 |    5920.55 |    1.350 |     0.977 | testfile.dat (1024MiB)
     1 |      3012341760 |       735435 |      23.94 |    6128.62 |    1.304 |     0.936 | testfile.dat (1024MiB)
     2 |      2908295168 |       710033 |      23.11 |    5916.94 |    1.351 |     0.984 | testfile.dat (1024MiB)
     3 |      3012657152 |       735512 |      23.94 |    6129.26 |    1.304 |     0.935 | testfile.dat (1024MiB)
     4 |      2907602944 |       709864 |      23.11 |    5915.53 |    1.351 |     0.975 | testfile.dat (1024MiB)
     5 |      3014549504 |       735974 |      23.96 |    6133.11 |    1.303 |     0.942 | testfile.dat (1024MiB)
     6 |      2911498240 |       710815 |      23.14 |    5923.45 |    1.349 |     0.982 | testfile.dat (1024MiB)
     7 |      3015483392 |       736202 |      23.96 |    6135.01 |    1.303 |     0.942 | testfile.dat (1024MiB)
-----------------------------------------------------------------------------------------------------
total:       23692500992 |      5784302 |     188.29 |   48202.48 |    1.327 |     0.959

Read IO
thread |       bytes     |     I/Os     |    MiB/s   |  I/O per s |  AvgLat  | LatStdDev |  file
-----------------------------------------------------------------------------------------------------
     0 |      1452736512 |       354672 |      11.55 |    2955.60 |    1.592 |     0.985 | testfile.dat (1024MiB)
     1 |      1505538048 |       367563 |      11.96 |    3063.02 |    1.534 |     1.007 | testfile.dat (1024MiB)
     2 |      1452081152 |       354512 |      11.54 |    2954.26 |    1.595 |     1.080 | testfile.dat (1024MiB)
     3 |      1506656256 |       367836 |      11.97 |    3065.30 |    1.532 |     0.936 | testfile.dat (1024MiB)
     4 |      1450803200 |       354200 |      11.53 |    2951.66 |    1.595 |     1.026 | testfile.dat (1024MiB)
     5 |      1507811328 |       368118 |      11.98 |    3067.65 |    1.532 |     1.062 | testfile.dat (1024MiB)
     6 |      1456005120 |       355470 |      11.57 |    2962.25 |    1.594 |     1.057 | testfile.dat (1024MiB)
     7 |      1505959936 |       367666 |      11.97 |    3063.88 |    1.533 |     1.045 | testfile.dat (1024MiB)
-----------------------------------------------------------------------------------------------------
total:       11837591552 |      2890037 |      94.08 |   24083.62 |    1.563 |     1.026

Write IO
thread |       bytes     |     I/Os     |    MiB/s   |  I/O per s |  AvgLat  | LatStdDev |  file
-----------------------------------------------------------------------------------------------------
     0 |      1457336320 |       355795 |      11.58 |    2964.96 |    1.109 |     0.906 | testfile.dat (1024MiB)
     1 |      1506803712 |       367872 |      11.97 |    3065.60 |    1.074 |     0.795 | testfile.dat (1024MiB)
     2 |      1456214016 |       355521 |      11.57 |    2962.67 |    1.107 |     0.808 | testfile.dat (1024MiB)
     3 |      1506000896 |       367676 |      11.97 |    3063.96 |    1.076 |     0.876 | testfile.dat (1024MiB)
     4 |      1456799744 |       355664 |      11.58 |    2963.86 |    1.108 |     0.854 | testfile.dat (1024MiB)
     5 |      1506738176 |       367856 |      11.97 |    3065.46 |    1.075 |     0.735 | testfile.dat (1024MiB)
     6 |      1455493120 |       355345 |      11.57 |    2961.21 |    1.105 |     0.831 | testfile.dat (1024MiB)
     7 |      1509523456 |       368536 |      12.00 |    3071.13 |    1.073 |     0.760 | testfile.dat (1024MiB)
-----------------------------------------------------------------------------------------------------
total:       11854909440 |      2894265 |      94.21 |   24118.86 |    1.091 |     0.822



total:
  %-ile |  Read (ms) | Write (ms) | Total (ms)
----------------------------------------------
    min |      0.190 |      0.100 |      0.100
   25th |      1.011 |      0.729 |      0.841
   50th |      1.405 |      0.985 |      1.161
   75th |      1.968 |      1.306 |      1.632
   90th |      2.434 |      1.656 |      2.187
   95th |      2.814 |      1.958 |      2.539
   99th |      4.075 |      3.153 |      3.779
3-nines |      7.703 |      7.140 |      7.472
4-nines |     23.842 |     21.344 |     22.874
5-nines |    132.133 |     73.863 |    125.933
6-nines |    141.497 |    131.922 |    137.809
7-nines |    145.285 |    135.744 |    145.285
8-nines |    145.285 |    135.744 |    145.285
9-nines |    145.285 |    135.744 |    145.285
    max |    145.285 |    135.744 |    145.285
```

In summary - 48K IOPS



## Building

### Building for Windows

```
 docker build -f Dockerfile.windows -t diskspd:ltsc2019 .
```

### Building for Linux

>> TODO

