# Optimizations

The goal is to reduce the size of the generated binaries. This is done by
aiding the linker to eleminate dead code by splitting large source files and
by hand-optimizing individual functions.

## Results

The sum of all these optimizations reduces the total size of Blink.ino by
more than 30%, saving more than 800 bytes of precious flash space:

   code	 |  data|    RAM|  flash total	|split stage
   ----  |  ----|   ----|   ----	|------------
   2507	 |   138|     72|   2645	|before split
   2450	 |   138|     72|   2588	|after splitting wiring.c
   2381	 |   138|     72|   2519	|after splitting wiring_digital.c
   1799	 |   138|     72|   1937	|after splitting HardwareSerial.c
   1686	 |   138|     72|   1824	|after optimizing pinMode()

Data is mostly the tables to map the Arduino pin numbers to the actual port
registers. RAM is mostly the transmit and receive buffers for serial
communication. These can't easily be optimized out.


## Splitting files

The SDCC linker does not detect unused functions and constants in an object
file. It always links the whole file even if only a single symbol contained
in the file is referenced. This results in pretty bloated binaries if the
SPL is used.

Splitting larger source files into smaller units and compile them
individually before building the libraries helps the SDCC linker to
eleminate dead code and to produce smaller binaries.

Splitting files is worthwhile for all SPL files and at least some of the
bigger Arduino core files.


### The stategy

The SPL consists of many source files with a very regular structure. This
allows to automate the splitting process with very little preparation work.

All SPL functions are documented with a doxygen comment block. The beginning
of this comment block is a line with only "/**", that can easily be used as
a marker for splitting the files.

Only the very first block is special. It contains definitions and prototypes
that are needed all over the module. This block is saved as a `.h` file and
`#include`'d by all the following blocks.

In most cases this is already sufficient. Only in very rare cases the
position of a split needs the be edited.

**To prevent a split**: Change the `/**` line into something different, `/***`
is used in the scripts.

**To force a split**: Add an empty Doxygen comment block:
```c
/**
 * This is just a split marker
 */
```


### Splitting the SPL files

Splitting and compiling the SPL libraries is moved into the separate project
[spl-splitter](https://github.com/tenbaht/spl-splitter) now.



### Split Arduino core files

The Arduino files don't have any regular structure. They need to be edited
to become splitable at all and some of the resulting split files still need
manual adjustments.

`wiring.c`, `wiring_digital.c` and `HardwareSerial.c` all compile into quite
large binaries. All of them are linked with almost 
every project. This is true even if no serial communication is used since
main.c references `serialEvent()` and this way pulls in all of
HardwareSerial.

Splitting these three files reduces the size of simple sketches
significantly. It does not help so much for complex sketches that use almost
all functions of these modules.



## Optimizing individual functions

`pinMode()` sticks out when looking at the size of individual functions. 270
bytes for just setting the IO-mode of a pin. A re-write in assember reduces
this to just 147 bytes.
