Fault resistant Bitsliced AES
-------------

Bitslicing is a technique to compute steps in an algorithm 1 bit at a time.  Each bit in a processor word would be a part
of a different data stream for that particular algorithm.  It is attractive because then it can run many different streams 
in parallel (depending on the word length).  E.g. a 32 bit word length can compute 32 different streams in parallel.

Because every stream is transposed, it becomes easy to enumerate them and decide which ones can act as redundant or
constant slices for detection of fault injections. 

You can see different implementations of the fault resistance added to the bitsliced implementation by looking at
the different branches:

* [unprotected](https://github.com/Secure-Embedded-Systems/fault-resistant-aes/tree/aes-unprotected)
* [redundant slices](https://github.com/Secure-Embedded-Systems/fault-resistant-aes/tree/master)
* [pipelined](https://github.com/Secure-Embedded-Systems/fault-resistant-aes/tree/pipelined)
* [shuffled pipelined](https://github.com/Secure-Embedded-Systems/fault-resistant-aes/tree/pipelined-shuffled)

See our paper:

C. Patrick, B. Yuce, N. Farhady Ghalaty, P. Schaumont, "Lightweight Fault Attack Resistance in Software Using Intra-Instruction Redundancy," Selected Areas in Cryptography (SAC 2016), St. John's, Canada, August 2016. 
	

Current results
-----------------


Performance measurements done for a fault resistant AES-CTR on a 64 bit 4 GHz Intel 4790 and compiled with GCC 4.8.4.
Overhead was calculated in reference to the [(unprotected) bitsliced implementation](https://github.com/conorpp/bitsliced-aes).

#### lightweight fault detection (i.e. minimal redundant slices)

|                      |footprint       | overhead | throughput     | overhead |
|----------------------|----------------|----------|----------------|----------|
|Performance optimized |  12,708 bytes  |  4.6%    |54.4 cycles/byte|   6.7%   |
|Footprint optimized   |  9,428 bytes   |   10.6%  | 83 cycles/byte |   2.5%   |


#### Maximum fault detection (i.e. half redundant slices, checking during every AES round)

|                      |footprint       | overhead | throughput      | overhead |
|----------------------|----------------|----------|-----------------|----------|
|Performance optimized |  12,708 bytes  |    4.6%  |111.8 cycles/byte|  119.2%  |
|Footprint optimized   |  9,428 bytes   |    10.6% |166.9 cycles/byte|  106.0%  |



The amount of redundant slices and number of rounds to check are decided during run time.  The idea is
to at first have a small fault detection at first to optimize performance but then increase redundancy
dynamically in the advent of a fault injection.



Performance could be improved by about 5-10x by writting in assembly and ensuring
more operations stay in registers rather then spill to memory.


Compiling
----------

Compile the benchmarking program by running:

```bash
make
```

Benchmark program requires OpenSSL.


Compile the test program by running:

```bash
make test
```

Compile it for measuring the AES implementation overhead:

```bash
make footprint
```

Change to the word length of your processor by editing the `WORD_SIZE` macro in bs.h.  Optimize for
footprint by using `-O2` instead of `-O3` in the Makefile and also deleting the `-DUNROLL_TRANSPOSE` 
flag.

# License

Copyright (c) 2016, Conor Patrick, Bilgiday Yuce, Nahid Farhady Ghalaty, Patrick Schaumont
All rights reserved.

C. Patrick, B. Yuce, N. Farhady Ghalaty, P. Schaumont, "Lightweight Fault
Attack Resistance in Software Using Intra-Instruction Redundancy," Selected
Areas in Cryptography (SAC 2016), St. John's, Canada, August 2016.

Redistribution and use in source and binary forms, with or without
modification, are permitted provided that the following conditions are met:

1. Redistributions of source code must retain the above copyright notice, this
   list of conditions and the following disclaimer.
2. Redistributions in binary form must reproduce the above copyright notice,
   this list of conditions and the following disclaimer in the documentation
   and/or other materials provided with the distribution.

THIS SOFTWARE IS PROVIDED BY THE COPYRIGHT HOLDERS AND CONTRIBUTORS "AS IS" AND
ANY EXPRESS OR IMPLIED WARRANTIES, INCLUDING, BUT NOT LIMITED TO, THE IMPLIED
WARRANTIES OF MERCHANTABILITY AND FITNESS FOR A PARTICULAR PURPOSE ARE
DISCLAIMED. IN NO EVENT SHALL THE COPYRIGHT OWNER OR CONTRIBUTORS BE LIABLE FOR
ANY DIRECT, INDIRECT, INCIDENTAL, SPECIAL, EXEMPLARY, OR CONSEQUENTIAL DAMAGES
(INCLUDING, BUT NOT LIMITED TO, PROCUREMENT OF SUBSTITUTE GOODS OR SERVICES;
LOSS OF USE, DATA, OR PROFITS; OR BUSINESS INTERRUPTION) HOWEVER CAUSED AND
ON ANY THEORY OF LIABILITY, WHETHER IN CONTRACT, STRICT LIABILITY, OR TORT
(INCLUDING NEGLIGENCE OR OTHERWISE) ARISING IN ANY WAY OUT OF THE USE OF THIS
SOFTWARE, EVEN IF ADVISED OF THE POSSIBILITY OF SUCH DAMAGE.

The views and conclusions contained in the software and documentation are those
of the authors and should not be interpreted as representing official policies,
either expressed or implied, of the FreeBSD Project.
