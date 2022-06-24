
# randcrack – Python random module cracker / predictor

[![Build Status](https://travis-ci.org/tna0y/Python-random-module-cracker.svg?branch=master)](https://travis-ci.org/tna0y/Python-random-module-cracker)
![PyPI](https://img.shields.io/pypi/v/randcrack.svg)
![PyPI - Python Version](https://img.shields.io/pypi/pyversions/randcrack.svg)
![PyPI - Implementation](https://img.shields.io/pypi/implementation/randcrack.svg)

This script is able to predict python's `random` module random generated values.

Script was tested against **Python 3.5.2**, **3.6.2.** and **3.7.0.** Should work against other versions of Python as well, since the generator is pretty much the same in **2.7.12**. Enjoy!

## Installation
To install randcrack, simply:

```bash
$ pip install randcrack
```

## How it works
The generator is based upon *Mersenne Twister*, which is able to generate numbers with excellent statistical properties(indistinguishable from truly random). However, this generator was not designed to be cryptographycally secure. You should NEVER use in critical applications as a PRNG for your crypto scheme.
You can learn more about this generator [on Wikipedia](https://en.wikipedia.org/wiki/Mersenne_Twister).

This cracker works as the following way. It obtains first 624 32 bit numbers from the generator and obtains the most likely state of Mersenne Twister matrix, which is the internal state. From this point generator should be synchronized with the cracker.

## How to use
It is **important to feed cracker exactly 32-bit integers** generated by the generator due to the fact that they will be generated anyway, but dropped if you don't request for them.
As well, you must feed the cracker exactly after new seed is presented, or after 624*32 bits are generated since every 624 32-bit numbers generator shifts it's state and cracker is designed to be fed from the begining of some state.

#### Implemented methods

Cracker has one method for feeding: `submit(n)`. After submitting 624 integers it won't take any more and will be ready for predicting new numbers.

Cracker can predict new numbers with following methods, which work exactly the same as their siblings from the `random` module but without `predict_` prefix. These are: `predict_getrandbits`, `predict_randbelow`, `predict_randrange`,  `predict_randint`, `predict_choice` and `predict_random`

Here's an example usage:
```python
import random, time
from randcrack import RandCrack

random.seed(time.time())

rc = RandCrack()

for i in range(624):
	rc.submit(random.getrandbits(32))
	# Could be filled with random.randint(0,4294967294) or random.randrange(0,4294967294)

print("Random result: {}\nCracker result: {}"
	.format(random.randrange(0, 4294967295), rc.predict_randrange(0, 4294967295)))
```
**Output**
```	
Random result: 127160928
Cracker result: 127160928
```
