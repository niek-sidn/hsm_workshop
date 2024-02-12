-------------
## Your own HSM!
SoftHSM2 is a software HSM (emulator?) by NLnet Labs that you can use
for free.\
To be installed on the server that needs to use it, **no networking
available, no true crypto module: secret keys are in RAM**.\
The creators of SoftHSM would like to enable networking, but lack time and resources.

To install:

Linux: install softhsm2 (apt: softhsm2, apk: softhsm-2.6.1-r4, yum/dnf:
"not found", roll our own: I got the recipe!

--------------------
## Exercise "Introducing SoftHSM2 by NLnet Labs"
```bash
apt install -y softhsm2
```
-   softhsm: you need to use sudo to use softhsm, if not softhsm only
    visible by user.
-   softhsm: softhsm2-util \--show-slots (always an empty slot available
    for a new token slot)
-   softhsm: man softhsm2-util (what is is, what does it do: list, init,
    delete, import. No signing/crypting!)
-   softhsm: softhsm2-util \--init-token \--free \--label \"Token1\"
    \--pin 0000 \--so-pin 1234 (PIN/\"password\" for user and so-user)
-   softhsm: softhsm2-util \--show-slots (show that a token has been
    inserted, and new free slot was created. \--free means: use first
    empty slot)
-   softhsm: NOTE no keys are in this token yet! (token is just the
    cryptomodule)
-   softhsm:  cat /etc/softhsm/softhsm2.conf (where are the tokens? And
    ls -lR /var/lib/softhsm/tokens/)
-   softhsm: there is an sqlite option, probably recompile needed.
-   softhsm2-util \--delete-token \--token Token1 (get rid of token, for
    deleting objects in the token see below)

-------------------
[Next](https://github.com/niek-sidn/hsm_workshop/blob/main/Slide12.md)
