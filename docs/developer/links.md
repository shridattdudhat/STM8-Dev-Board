# Links

## Further reading and application notes

A very good and compact primer on
[STM8S-Programming](https://github.com/TG9541/stm8ef/wiki/STM8S-Programming).
Does not rely on any external libraries.

[A series of
articles](https://lujji.github.io/blog/bare-metal-programming-stm8/) on bare
metal programming of the STM8S CPUs. By far the best introduction I
found so far. It would have helped me a lot if it would have around at the
time when I started this project.

[Quick introduction to the Arduino.mk
makefile](http://hackaday.com/2015/10/01/arduino-development-theres-a-makefile-for-that/)
on hackaday.com

[PM0051](http://www.st.com/resource/en/programming_manual/cd00191343.pdf):
STM8AF Flash programming manual  
[UM0470](http://www.st.com/resource/en/user_manual/cd00173911.pdf):
STM8 SWIM protocol and debug manual  
[AN2658](http://www.st.com/resource/en/application_note/cd00176594.pdf):
Using the analog-to-digital converter of the STM8S microcontroller  

Many [examples and presentations about the
STM8S](https://github.com/VincentYChen/STM8teach)

It contains the [SPL examples from
ST](https://github.com/VincentYChen/STM8teach/tree/master/code/Project/STM8S_StdPeriph_Examples),
one of the most useful resources on the STM8.

[Hardware and pinouts of several ST-Link compatible flash
tools](https://wiki.cuvoodoo.info/doku.php?id=jtag)

[Using the
ADC](http://blog.mark-stevens.co.uk/2012/09/single-scan-adc-on-the-stm8s/)
by Mark Stevens

[Example for RS-232 handling with
SPL](https://sourceforge.net/p/oggstreamer/oggs-stm8-firmware-001/ci/master/tree/rx_ringbuffer.c)

[AN3139](http://www.st.com/resource/en/application_note/cd00262293.pdf):
Migration guideline within the STM8L familiy  



## Similar or related projects

None of these projects are related to or part of Sduino. They are written
independently, but with a similar goal in mind: To simplify STM8 programming
for the beginner.

[STM8Sduino](https://github.com/dannyf00/STM8Sduino): A minimalistic
approach to adopt parts of the Arduino-API to the STM8. Based on IAR and
COSMIC compiler (Windows only). Port to SDCC shouldn't be too hard but is
not done yet. Low overhead, but basic functions only. No support for porting
Arduino libraries.


## Other interesting STM8-related projects

[gdb support for the STM8](https://stm8-binutils-gdb.sourceforge.io/)
STM8 toolchain with GDB debugger and SDCC. Use the hardware debugging
feature of the STM8 with ST-Link and gdb.

[STVD-SDCC integration suite](https://github.com/shkolnick-kun/stvd-sdcc)

