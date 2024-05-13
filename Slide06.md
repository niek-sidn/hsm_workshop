------------------------------
## What's in it?
At a minimum, an HSM appliance should contain a **crypto module**.\
This is a closed-off 'mini computer' with its own CPU and RAM and
storage inside the appliance/card/stick.\
This module handles all encryption and signing, also stores the keys (or
maybe a key to all keys).\
All "other stuff" like e.g. networking and api-interfacing is handled
by parts outside this crypto module.\
The crypto module in most cases has a very limited interface.

An HSM should contain an HRNG (hardware random number generator),\
because key generation is very dependent on a good source of random numbers.

A Linux/Windows server in most cases has no HRNG, but it uses tricks to
create real random.\
E.g. harvesting the Xth decimal of all execution times of processes, or
in the past also timings of key-presses or mouse movement (that was useless on a headless server...).\
A good key generator will use a blocking random device and wait for new
real random bits if this device cannot deliver yet.\
Especially when creating keys: a predictable key may lead to the worst false sense of security.

------------
[Next](https://github.com/niek-sidn/hsm_workshop/blob/main/Slide07.md)

## Exercise "Random in bash"
Random: bash random commands use (non-blocking) /dev/urandom, not (almost) real random.\
Run:
```bash
RANDOM=13 && echo $RANDOM
```
You got 21880, didn't you?

---
Now run twice:
```bash
echo $RANDOM
```
You got 9438 and 11869 , didn't you?

---
Now do *both* previous exercises again!

/dev/urandom generates a predictable, repeatable set of pseudo-random numbers.\
The begin argument is a starting point, and this can of course be a piece of "good" random from the pool.\
But it can also be the output of 'date' at reboot.\
Note: For testing a predictable, repeatable set of pseudo-random numbers can actually be a good thing!

---
Linux does have real random: /dev/random (but it could be blocking)\
Real random:
```bash
cat /proc/sys/kernel/random/poolsize
```
Used to be 4096, but is now 256 (on kernels >5, a switch was made)

---
Available random:
```bash
cat /proc/sys/kernel/random/entropy\_avail
```
On kernels <5 this drops on intensive use of /dev/random (e.g. `od -d /dev/random`)

---

**After** the PKSC11 exercises you can revisit this exercise and try:
```
pkcs11-tool --module /usr/lib/softhsm/libsofthsm2.so --token Token1 --generate-random 64 | xxd -c 64 -p
```
Here we see random from an HSM

---------------
[Next](https://github.com/niek-sidn/hsm_workshop/blob/main/Slide07.md)
