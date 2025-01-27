
# Introduction

This project contains three case studies of our [RabbitFX](https://github.com/RabbitBio/RabbitFX) project:

 - [RabbitFX-Ktrim](./RabbitFX-Ktrim)
 - [RabbitFX-Mash](./RabbitFX-Mash)
 - [RabbitFX-fastp](./RabbitFX-fastp)

# RabbitFX-Ktrim
RabbitFX-Ktrim is an enhanced version of [Ktrim](https://github.com/hellosunking/Ktrim) based on [RabbitFX](https://github.com/RabbitBio/RabbitFX).

## Installation

you can re-compile the programs:

```bash
git https://github.com/RabbitBio/RabbitFX-Casestudy.git
cd RabbitFX-Casestudy/RabbitFX-Ktrim
mkdir build
cd build
cmake ..
make 
```

## Usage

```bash
Usage: RabbitFX-Ktrim [options] -1/-U Read1.fq [ -2 Read2.fq ] -o out.prefix

RabbitFX-Ktrim is an enhanced version of Ktrim based on RabbitFX.

Compulsory parameters:

  -1/-U Read1.fq  Specify the path to the files containing read 1
                  If your data is Paired-end, use '-1' and specify read 2 files using '-2' option
                  Note that if '-U' is used, specification of '-2' is invalid
                  If you have multiple files for your sample, use ',' to separate them
                  Gzipped files are supported from version 1.2.0

  -o out.prefix   Specify the prefix of the output files
                  Note that output files include trimmed reads in FASTQ format and statistics

Optional parameters:

  -2 Read2.fq     Specify the path to the file containing read 2
                  Use this parameter if your data is generated in paired-end mode
                  If you have multiple files for your sample, use ',' to separate them
                  and make sure that all the files are well paired in '-1' and '-2' options

  -t threads      Specify how many threads should be used (default: 1, single-thread)
                  You can set '-t' to 0 to use all threads (automatically detected)

  -p phred-base   Specify the baseline of the phred score (default: 33)
  -q score        The minimum quality score to keep the cycle (default: 20)
                  Note that 20 means 1% error rate, 30 means 0.1% error rate in Phred

  -w window       Set the window size for quality check (default: 5)
                  Ktrim will stop when all the bases in a consecutive window pass the quality threshold

                  Phred 33 ('!') and Phred 64 ('@') are the most widely used scoring system
                  Quality scores start from 35 ('#') in the FASTQ files is also common

  -s size         Minimum read size to be kept after trimming (default: 36; must be larger than 10)

  -k kit          Specify the sequencing kit to use built-in adapters
                  Currently supports 'Illumina' (default), 'Nextera', 'Transposase' and 'BGI'
  -a sequence     Specify the adapter sequence in read 1
  -b sequence     Specify the adapter sequence in read 2
                  If '-a' is set while '-b' is not, I will assume that read 1 and 2 use same adapter
                  Note that '-k' option has a higher priority (when set, '-a'/'-b' will be ignored)

  -m proportion   Set the proportion of mismatches allowed during index and sequence comparison
                  Default: 0.125 (i.e., 1/8 of compared base pairs)

  -h/--help       Show this help information and quit
  -v/--version    Show the software version and quit

RabbitFX-Ktrim: enhanced extra-fast and accurate adapter- and quality-trimmer based on Ktrim.

```
### Example 1

Your data is generated using Illumina TruSeq kit in Single-end mode, then you can run:

```bash
./RabbitFX-Ktrim -U /path/to/read1.fq -o /path/to/output/dir
```

### Example 2

Your data is generated using a kit with customized adapters; your data is composed of 3 lanes in Paired-end
mode and uses Phred scoring system starts from 35; you want to keep the high quality (Phred score >=30)
bases and reads longer than 50 bp after trimming; and you want to use 4 threads to speed-up the analysis,
then you can run:

```bash
./RabbitFX-Ktrim -1 /path/to/lane1.read1.fq.gz,/path/to/lane2.read1.fq.gz,/path/to/lane3.read1.fq \
                  -2 /path/to/lane1.read2.fq.gz,/path/to/lane2.read2.fq.gz,/path/to/lane3.read2.fq \
                  -t 4 -p 35 -q 30 -s 50 -o /path/to/output/dir \
                  -a READ1_ADAPTER_SEQUENCE -b READ2_ADAPTER_SEQUENCE
```


# RabbitFX-fastp
RabbitFX-fastp is an enhanced version of [fastp](https://github.com/OpenGene/fastp) based on [RabbitFX](https://github.com/RabbitBio/RabbitFX).

# Build

we only provide the linux version of RabbitFX-fastp

```bash
cd RabbitFX-fastp && make
```
# Simple usage
* For single end data 
```
RabbitFX-fastp -w nthreads -i in.fq -o out.fq
```
* For paired end data
```
RabbitFX-fastp -w nthreads -i in.R1.fq -I in.R2.fq -o out.R1.fq -O out.R2.fq
```

# RabbitFX-Mash

## Build

**Dependencies:**

   - Cap'n Proto ( https://capnproto.org/ )
   - GNU Scientific Library ( http://www.gnu.org/software/gsl/ )
   - Zlib ( included with most Linuxes, http://www.zlib.net ) 
   - GCC >= 5 (C++14 required)

**Build:**

```bash
cd RabbitFX-Mash
./bootstrap.sh
./configure [--prefix=...] [--with-capnp=...] [--with-gsl=...] \
            [--with-simd=yes/no]
make -j4
#optional
make install
#optional
make test
```

**Build dependency-free binary:**

```bash
cd RabbitMash
./bootstrap.sh
./configure [--prefix=...] [--with-capnp=...] [--with-gsl=...] \
            [--with-simd=yes/no] [--enable-static-gsl=yes]     \
            [--enable-static-cpp=yes]
make -j4
#optional
make install
#optional
make test
```

You can also type `./configure -h` for configure help information.

**Install dependency on CentOS 8.1 (root user):**

```bash
sudo dnf install capnproto capnproto-devel gsl gsl-devel
```

If you are not a root user, you need to build the dependecies from source code.


## Simple Usage

**sketch:**

```bash
./mash sketch -i test/genome1.fna -p nthreads -o test/genome1.fna.msh
```

**screen:**

```bash
./mash screen test/genome1.fna.msh test/reads1.fastq -p nthreads > scr.out
```

## Cite
H. Zhang et al., "RabbitFX: Efficient Framework for FASTA/Q File Parsing on Modern Multi-Core Platforms," in IEEE/ACM Transactions on Computational Biology and Bioinformatics, vol. 20, no. 3, pp. 2341-2348, 1 May-June 2023, doi: 10.1109/TCBB.2022.3219114.
