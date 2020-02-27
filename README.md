# TESTU01_gateway

This project provides two things to make it easy to use [TestU01](http://simul.iro.umontreal.ca/testu01/tu01.html)
random number generator test suite:

- a simple executable consumes 32 bits integer (encoded in hexa) from the standard input, and passing them to TestU01.
- a docker image containing this executable

This makes it easy to test the quality of a RNG (random number generator) regardless of the platform / language.

## Example of usage

```python
# examples/test_rng_example.py
from random import randint
import sys
from struct import pack

write = sys.stdout.buffer.write
limit = 2**32-1
while True:
    write(pack("=I", randint(0,limit)))
```

This small script just output an endless stream of 32 bits random number 
in binary (4 bytes per number).

To test the RNG, we just pipe the output of the script to test 
`testu01_gateway` executable in the docker image:

```bash
python3 test_rng_example.py | docker run --rm -it blep/test01:latest --small-crush
```

## Command-line

```
testu01_gateway reads number from stdin and forwards them to TestU01
selected battery of tests.

Each generated 32 bits number must be serialized to stdin as 4 bytes.

Makes sure to run tests twice: once with and once without --reverse-bits.
            
Options:
--name: set the name of the random number generator (used in final report).
-s, --small-crush  : run SmallCrunch tests.
-c, --crush        : run Crunch tests (the default battery of test).
-b, --big-crush    : run BigCrunch tests.
-r, --reverse-bits : reverse bits of the decoded 32 bits integer (bit 0 is 
                     swapped with bit 31, bit 1 with bit 30...).
-h, --help         : show this help text.
```

## Docker image

The docker image is available on [dockerhub](https://github.com/blep/testu01_gateway)
and published by dockerhub's trusted build system (dockerhub compiles and publishes
the image directly from the github sources).

## Understanding how to use TestU01

See this [PCG Blog Entry](http://www.pcg-random.org/posts/pcg-passes-practrand.html).

## Building from the source

Install C++ development dependencies:

```bash
apt-get install git git-gui cmake g++ python3
```

From the source root directory:

```bash
mkdir build && cd build && cmake -G "Unix Makefiles" .. && make
```

## Licensing

Content of this repository has the MIT license.

It relies on TestU01 library that has its own [license](http://simul.iro.umontreal.ca/testu01/copyright.html).
