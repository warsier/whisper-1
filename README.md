
# WHISPER 1.0 with PMTest injection

Here we provide the Whisper repository with PMTest persistency testing API. We add our testing code to the original Whisper project and test the persistency of the code. We modified the following workloads in Whisper:
* mnemosyne-gcc
* nstore
* nvml
* PMFS-new
* redis

To build the entire repository:
First, mnemosyne-gcc have a few extra dependencies and need to be met. Also, ALPS needs to be compiled individually. Please refer to the [README](https://github.com/warsier/mnemosyne-gcc/blob/a27ac7dba1c2ad3f074fef06f54319b8464775c3/README.md) for more details.
Then the entire repository can be built using:
~~~
	$ cd whisper
	$ ./script.py -b -w all
~~~
Please refer to the original document for further details.


**WHISPER**, or **Wisconsin-HPL Suite for Persistence** is a comprehensive benchmark
suite for emerging persistent memory technologies. For more details :

**An Analysis of Persistent Memory Use with WHISPER**. *ASPLOS'17*.

Sanketh Nalli, Swapnil Haria, Mike Swift, Mark Hill, Haris Volos, Kim Keeton.

research.cs.wisc.edu/multifacet/whisper/

## IMPORTANT
We are currently testing WHISPER in various environments.
PLEASE USE AT YOUR OWN RISK.
Report issues and suggestions to Swapnil (swapnilh at cs dot wisc edu) or Sanketh (sankey
at cs dot wisc dot edu). 

Eliza is a tool to process traces generated by WHISPER applications.
You may use it or develop your own for your analysis.

## To download:
~~~
   	$ git clone --recursive https://github.com/warsier/whisper.git
	$ cd whisper
~~~
OR 
~~~
   	$ git clone https://github.com/warsier/whisper.git
	$ cd whisper
	$ ./script.py -d
~~~

## To build:
~~~
	$ cd whisper
	$ ./script.py -b \
		-w	{ycsb,tpcc,echo,redis,ctree,hashmap,memcached,vacation,nfs,exim,sql,all}
~~~

## To run (tracing is optional, but need to be root for it):
~~~
	$ cd whisper
	$ ./script.py -r [-t] -z {small|med|large} \
		-w  {ycsb,tpcc,echo,redis,ctree,hashmap,memcached,vacation,nfs,exim,sql,all}
~~~

## To clean:
~~~
	$ cd whisper
	$ ./script.py -c \
		-w {ycsb,tpcc,echo,redis,ctree,hashmap,memcached,vacation,nfs,exim,sql,all}
~~~

## To update:
~~~
	$ cd whisper
	$ ./script.py -u
	git submodule update --remote
	git submodule update status

~~~

## For help:
~~~
	$ cd whisper
	$ ./script.py -h

usage: script.py [-h] [-d] [-b] [-r]
              [-w {ycsb,tpcc,echo,redis,ctree,hashmap,memcached,vacation,nfs,exim,sql,all}]
              [-z {small,med,large}] [-t] [-c] [-s] [-u] [-p]

Buildi, Clean, Run, Update WHISPER

optional arguments:
  -h, --help            show this help message and exit
  -d                    Download workload
  -b                    Build workload
  -r                    Run workload
  -w {ycsb,tpcc,echo,redis,ctree,hashmap,memcached,vacation,nfs,exim,sql,all}
                        Workload
  -z {small,med,large}  Set workload size
  -t                    Enable tracing. Need to be root.
  -c                    Clean workload
  -s                    Clean workload and dependencies
  -u                    Update benchmark
  -p                    More help for a workload

~~~

## For more help:
~~~
Example,
	$ ./script.py -w vacation -p 

Usage: ./build/bench/stamp-kozy/vacation/vacation [options]

Options:                                             (defaults)
    c <UINT>   Number of [c]lients                   (1)
    n <UINT>   [n]umber of user queries/transaction  (10)
    q <UINT>   Percentage of relations [q]ueried     (90)
    t <UINT>   Number of [t]ransactions              (131072)
    r <UINT>   Number of [r]ows		       (5767168)
    u <UINT>   Percentage of [u]ser transactions     (80)
    e <UINT>   Enable trac[e] collection             (0)

~~~

## FAQs :

#### 1. For Nstore-{YCSB, TPCC} and Echo I get a runtime error that says "no free memory of size 128 available". 
#### What do I do ?

Nstore and Echo create a file in /dev/shm (zfile for nstore and efile for echo}
which act as persistent memory pools. So does every application in WHISPER.
The default size is 1GB. When space is low,
the above error is thrown. We will make this a run time parameter in the future. For now, simply increase it by altering the PSEGMENT_RESERVED_REGION_SIZE
in Nstore and Echo. You may find this variable by using grep, cscope or any indexing tool for browsing source code. 
Its location varies with application. Delete the old pool, recompile and re-run. Suggested sizes are as shown.
Please pay attention to the format of defining the size i.e. XXX * 1024 * 1024 * 1024. This is in bytes.

~~~
#define PSEGMENT_RESERVED_REGION_SIZE (2UL * 1024 * 1024 * 1024) // 2GB
#define PSEGMENT_RESERVED_REGION_SIZE (4UL * 1024 * 1024 * 1024) // 4GB
#define PSEGMENT_RESERVED_REGION_SIZE (8UL * 1024 * 1024 * 1024) // 8GB
~~~

You may also reduce the size as follows.

~~~
#define PSEGMENT_RESERVED_REGION_SIZE (512 * 1024 * 1024) // 512MB
#define PSEGMENT_RESERVED_REGION_SIZE (256 * 1024 * 1024) // 256MB
#define PSEGMENT_RESERVED_REGION_SIZE (128 * 1024 * 1024) // 128MB
~~~
