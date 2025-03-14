---------------------
## Salting
When signing/encrypting/hashing also some random is sometimes needed. This
is commonly called a 'salt' or an 'initialization vector'.\
This random could be generated by an HSM, but is never stored in the
HSM (like a key is).

Why salt or IV? The more you use a key, the more output of your key is
available to the crackers, the higher the risk becomes of them finding
the key. This is known as an "offline, stored dictionary attack", just
pre-calculate (and store and index) all possible options...\
Also with e.g. AES, without a bit of random the encrypted text will be 
the same if the input was the same: 
The enemy will probably recognize your "Attack at dawn!" message.

With different salting, even identical input and an identical key will
give you different output every time. And this salt doesn't even need to be a secret.\
You can view a salt as a kind of password/key extender: a longer password
makes pre-calculation more difficult even if the extension is not secret.\
So put a little random into a signature/encryption, this will make a
stored dictionary attack much more difficult, even pseudo random will
do.\
E.g. NSEC3 can be salted. But the latest RFC advices against it:
> "In the case of DNS, the situation is different because the hashed
> names placed in NSEC3 records are always implicitly 'salted' by
> hashing the FQDN from each zone."\
[source](https://datatracker.ietf.org/doc/html/rfc9276#name-salt)

[Next](https://github.com/niek-sidn/hsm_workshop/blob/main/Slide08.md)

## Exercise "Finland is salty" (could it be the Salmiakki?)
Install dig or kdig on your computer or VM or container:
```bash
apt install knot-dnsutils
OR
apt install dnsutils
```
Issue this command (or your OS's equivalent):
```bash
kdig +dnssec +multi nsec3param fi. @1.1.1.1
```

----------------------
Now do .nl

--------------------
Now do .se\
(Hint: if the penny doesn't drop, do blah-nonexistant.se.)

------------------------
[Next](https://github.com/niek-sidn/hsm_workshop/blob/main/Slide08.md)
