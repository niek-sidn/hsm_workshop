-------------
## Your very own HSM!
**SoftHSM2** is a software HSM (emulator?) by NLnet Labs that you can use
for **free**. [link](https://github.com/opendnssec/SoftHSMv2)\
To be installed on the server that needs to use it, **no networking
available, no true crypto module: secret keys are in RAM**.\
Nice: SoftHSM is a **drop in** for a real HSM, this means you have to change very little when switching to a real HSM.

Often included in your distribution: (apt: softhsm2, apk: softhsm-2.6.1-r4)
If not, you can roll our own: Here is the [recipe](https://github.com/niek-sidn/hsm_workshop/blob/main/Build_SoftHSM.md)!

--------------------
## Exercise "Introducing SoftHSM2 by NLnet Labs"
```bash
**sudo -i**
apt update
apt install -y softhsm2 man sudo
usermod -aG softhsm <your username>
```
------------
SoftHSM has virtual **"slots"**, in which **"tokens"** can be placed (like a card reader or USB port)\
***Token is just the term used for a device or virtual device that can do crypto operations, (like a smartcard or an USB-HSM).***\
A token can contain lots of key objects.\
[illustration](https://github.com/tpm2-software/tpm2-pkcs11/blob/master/docs/illustrations/reader-slot-token-obj.png)
```bash
sudo softhsm2-util --show-slots
```
Slots are numbered, but there always an empty slot available for a new token slot.

**Note:** Here you meet for the first time a **vendor tool** for using an HSM.\
SoftHSM -> softhsm2-util\
Thales -> lunacm, vtl, etc.\
NitroKey -> nitropy\
Also: OpenDNSSEC -> ods-hsmutil (not an HSM vendor, also: the HSM is not addressed directly)\
Also: Knot -> keymgr (not an HSM vendor, also: the HSM is not addressed directly)

---------------------------------

RTFM:
```bash
man softhsm2-util
```
Here you read what it is, and what it does: list, init, delete, import, user management.
But you'll see **no 'encrypt' or 'sign' commands** and you cannot just give SoftHSM a file and expect it to be signed or encrypted.
You need middleware, more on that later!

-------------
Your first token
```bash
sudo softhsm2-util --init-token --free --label "Token1" --pin 0000 --so-pin 1234
sudo softhsm2-util --show-slots
```
The pin you see is a PIN/password for using the token, but why twice? Because there are 2 users/roles in SoftHSM.
In SoftHSM the normal user can do crypto operations using the key objects in the token, and create or destroy tokens.
In SoftHSM the Security officer ("SO", more on this later) can reinitialise a token, not much else can be known from the documentation.
When you did the show-slots, you saw that a token has been inserted (with an unexpected number), and a new free slot was created (also with an unexpected number).
(--free just means: use first empty slot, so you do not have to look first)

-------------
**NOTE** no keys are in this token yet! The token is just the cryptomodule, and softhsm2-util is not the tool for creating keys.
```bash
cat /etc/softhsm/softhsm2.conf
ls -lR /var/lib/softhsm/tokens/
```
Note: if you compiled SoftHSM yourself, according to my recipe, you should have an Sqlite3 db in /var/lib/softhsm/tokens/.\
      try: sqlite3 /var/lib/softhsm/tokens/....../sqlite3.db and command .dump to see all (hint: .quit/.help).

Please note: SoftHSM isolates its slots/tokens, a token created by a user is unavailable to other users.
This means that if you are creating tokens for a different user (e.g. Bind, Knot, OpenDNSsec) you need to use sudo. You'll learn this later.

-------------------
[Next](https://github.com/niek-sidn/hsm_workshop/blob/main/Slide12.md)
