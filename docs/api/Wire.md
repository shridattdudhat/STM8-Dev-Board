# Wire

This is an improved version of the stock Arduino Wire library for I2C
communication. It shares most of the hardware related code with the re-write
of the [Sduino I2C library](I2C.md), but holds on to the Arduino API.

The main difference between this STM8 version and the Arduino AVR version is
the existance of a timeout function to prevent deadlocks due to failed
communication. Polling mode is used for communication, interrupts are not
supported (yet).

So far, only Master Transmit and Master Receive modes are supported, slave
modes are not implemented yet.


## API

This is a pre-instantiated singleton library. It is not possible to use more
than one instance per sketch or to change the instance name.

The API syntax is very similar to the original C++ syntax. Some name
mangeling was needed to distinguish the different variants of the `write()`
method. Apart from this replacing the dots in the method names for
underscores is all it needs.



Arduino syntax				|sduino syntax		|real function name
--------------------			|---------------------	|-------------
`Wire.begin()`				|`Wire_begin()`			|same
`Wire.begin(ownaddress)`		|slave mode not supported yet|-
`Wire.end()`				|`Wire_end()`			|same
`Wire.setClock(clock)`			|`Wire_setClock(clock)`		|same
not implemented				|`Wire_timeOut(millisec)`	|same
`Wire.beginTransmission(addr)`		|`Wire_beginTransmission(addr)`	|same
n = `Wire.endTransmission()`		|n = `Wire_endTransmission()`	|same (inline)
n = `Wire.endTransmission(stop)`	|n = `Wire_endTransmission1(stop)`|same
`Wire.write(val)`			|`Wire_write(val)`		|same
`Wire.write(*str)`			|`Wire_write_s(*str)`		|same
`Wire.write(*data, len)`		|`Wire_write_sn(*data, len)`	|same
`n = Wire.available()`			|`n = Wire_available()`		|same
`val = Wire.read()`			|`val = Wire_read()`		|same
`Wire.flush()`				|`Wire_flush()`			|same
`val = Wire.peek()`			|`val = Wire_peek()`		|same
`Wire.requestFrom(addr, n)`		|`Wire_requestFrom(addr, n)`	|`Wire_requestFrom2`
`Wire.requestFrom(addr, n, sendStop)`	|`Wire_requestFrom(addr, n, sendStop)`	|`Wire_requestFrom3`
`Wire.requestFrom(addr, n, iaddr, isize, sendStop)`|`Wire_requestFrom(addr, n, iaddr, isize, sendStop)`	|`Wire_requestFrom5`
`Wire.print(*str)`	|`Wire_print_s(*str)`		|`Print_print_s(Write_write1,*str)`
`Wire.print(*data,len)`	|`Wire_print_sn(*data,len)`	|`Print_print_sn(Write_write1,*data,len)`
`Wire.print(long)`	|`Wire_print_i(long)`		|`Print_print_i(Write_write1,long)`
`Wire.print(ulong)`	|`Wire_print_u(ulong)`		|`Print_print_u(Write_write1,ulong)`
`Wire.print(long,base)`	|`Wire_print_ib(long,base)`	|`Print_print_ib(Write_write1,long,base)`
`Wire.print(ulong,base)`|`Wire_print_ub(ulong,base)`	|`Print_print_ub(Write_write1,ulong,base)`
`Wire.print(double)`	|`Wire_print_f(float)`		|`Print_print_f(Write_write1,float)`
`Wire.print(double,dig)`|`Wire_print_f(float,dig)`	|`Print_print_fd(Write_write1,float,dig)`
`Wire.println()`	|`Wire_println()`		|`Print_println(Write_write1)`
`Wire.println(*str)`	|`Wire_println_s(*str)`		|`Print_println_s(Write_write1,*str)`
`Wire.println(*data,len)`|`Wire_println_sn(*data,len)`	|`Print_println_sn(Write_write1,*data,len)`
`Wire.println(long)`	|`Wire_println_i(long)`		|`Print_println_i(Write_write1,long)`
`Wire.println(ulong)`	|`Wire_println_u(ulong)`	|`Print_println_u(Write_write1,ulong)`
`Wire.println(long,base)`|`Wire_println_ib(long,base)`	|`Print_println_ib(Write_write1,long,base)`
`Wire.println(ulong,base)`|`Wire_println_ub(ulong,base)`|`Print_println_ub(Write_write1,ulong,base)`
`Wire.println(double)`	|`Wire_println_f(float)`	|`Print_println_f(Write_write1,float)`
`Wire.println(double,dig)`|`Wire_println_f(float,dig)`	|`Print_println_fd(Write_write1,float,dig)`


Please note the polymorphism of the `Wire_requestFrom()` method. This is a
special case and only possible for pre-instantiated singleton libraries.

Due to the way sdcc invokes the preprocessor sdcpp the same trick doesn't
work for functions without arguments - so we still need
`Wire_endTransmission()` and `Wire_endTransmission1(stop)`. Hopefully, this
inconsistency does not spread more confusion than it is worth.


## Timeout

The timeout functionality was added for the Sduino port and is not part of
the original library. The maximum transmission length is limited by the size
of the data buffer of 32 bytes. At 100kHz this would take aprox. 3ms (1
address byte, 32 data bytes, 1 ACK bit after every byte).

The default timeout is set to 20ms, that should work for most cases. If
using a very slow device, the timeout can be changed using the
`Wire_setTimeout()` function any time after initialzing the library by
calling `Wire_begin()`. The timeout is defined in ms, so the maximum value
for this 16 bit value represents about 65s - more than a minute.

The value zero disables the timeout feature.


## Further reading

Programming the I2C communication on the register level is quite complex for
the STM8, especially in receive mode. The most detailed information on this
topic is the

- application note AN3281 bundeled with
- the related source code package [STSW-STM8004](https://www.st.com/en/embedded-software/stsw-stm8004.html)

Downloading from the ST website requires a (free) registration. Somebody
uploaded the full package to a [github
repository](https://github.com/jiaohaitao/stsw-stm8004)

There are some important notes on I2C implementation in the errata sheets:

- [STM8S001J3/003xx/103xx/903xx Errata sheet, rev. 5
  (CD00265449)](https://www.st.com/content/ccc/resource/technical/document/errata_sheet/c9/f9/ef/bf/63/91/4a/1f/CD00265449.pdf/files/CD00265449.pdf/jcr:content/translations/en.CD00265449.pdf)
- [STM8S005xx STM8S105xx Errata sheet, rev. 7
  (CD00270741)](https://www.st.com/content/ccc/resource/technical/document/errata_sheet/e3/c3/4e/24/0c/ca/4b/e7/CD00270741.pdf/files/CD00270741.pdf/jcr:content/translations/en.CD00270741.pdf)
- [STM8S007xx STM8S20xxx Errata sheet, rev. 6
  (CD00244749)](https://www.st.com/content/ccc/resource/technical/document/errata_sheet/7a/94/8f/fe/84/14/41/6d/CD00244749.pdf/files/CD00244749.pdf/jcr:content/translations/en.CD00244749.pdf)
