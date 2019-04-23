# bwa-mem2

A. cd bwa-mem2/
We recommend you to directly goto point 'E' and run the test script. 

Else, bwa-mem depends on seqAn library, you can manually download it as:
"wget http://packages.seqan.de/seqan-library/seqan-library-2.1.1.tar.xz" and untar it.


B. Compile:
1. make  

'make' detects the underlying harware flags for AVX512 or AVX2 vector modes and compiles accordingly.
If the platform does not support any AVX vector mode then it compiles the code in fully scalar mode.
For example, Intel Xeon Haswell supports AVX2 vector mode; Intel Xeon Skylake supports AVX512
vector mode. AVX vector support by the processor can be check in /proc/cpuinfo file.


C. Run:
- bwa-mem2 is the executable file created after compilation.
- First create the index using 'bwa-mem2 index'
- Then 'bwa-mem2 mem' can be run appropriate paramters.

Notes:
- There is only one difference/addtion to commandline paramters of BWA-MEM.
- The output sam filename is provided using mandatory command-line option: '-o'
- Remaining command-line parameters are supported as it is.

---------------------
Example run:
- $ bwa-mem2 index datasets/hg38.fa   
- $ bwa-mem2 mem -t 1 -o datasets/aln-se.sa datasets/hg38.fa datasets/SRR099967.filt.fastq
----------------------


D. Directory structure (bwa-mem2):
- src/ - contains source code files.
- test/ - contains test files to run individual benchmarks of smem, sal, bsw (disabled for now).
- testdata/ - small read set (250K SE reads) to test-run the code (testRun_bwamem2 downloads it).
- testRun_bwamem2.sh - shell script to run the code on testdata (reads). The script requires path to store the reference genome as parameter.


E. Test shell scipt:
./testRun_bwamem2.sh <path_to_a_dir>

1. It downloads hg37.fa (human reference genome) and seqAn library (dependency) to the given directory
2. It then creates the index (>70GB for human genome)
3. The script compiles the code.
4. It executes the code on testdata (downloads the testdata) and stores the output in 'output/' dir.
5. It reports the correctness of the generated sam output.

----------------------------------------------------------------------------------
VERY IMPORTANT:
1. 'bwa-mem2 index' requires ~70GB of space for full genome.
2. To avoid expensive mallocs during kernel computations, we pre-allocate a chunk of memory for all three kernels (fastmap.cpp). We use macros (defined in macro.h) to allocate an estimated memory chunk. So, in any case if failure of 'asserts' related to memory allocation, during execution, please increase the values of the corresponding macros (placed in macro.h).
3. If you are running out of space, please decrease the values of the macros (in macro.h).
4. We recommend to run the code in AVX512 mode to get maximum performance.

----------------------------------------------------------------------------------
Notes:
- As mentioned in point 'B', the code supports three exceution modes:
  1. Scalar: It runs bwa-mem2 with the kernels running with scalar instructions
  2. Vector AVX2: It runs bwa-mem2 with the kernels running AVX2 vector instructions (256 bits vector-width)
  3. Vector AVX512: It runs bwa-mem2 with the kernels running AVX512 vector instructions (512 bits vector-width)

- BWA-MEM2 displays run-time performance profiling of the code on standard output. (Hopefully, we managed it to be self-explanatory)

----------------------------------------------------------------------------------

