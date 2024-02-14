---------------------------------
## PKCS\#11 \#2
As mentioned before PKCS#11 works with "Slots" and "Tokens".
[Also illustrated here](https://github.com/tpm2-software/tpm2-pkcs11/blob/master/docs/illustrations/reader-slot-token-obj.png)\
In PKSC11 "Token" is the term used for a device that can do crypto operations, e.g. a smartcard 
or the crypto module of an HSM or an HSM partition (virtual HSM on e.g. Thales Luna).\
USB devices for OTP and other things that go into a slot are often also called "tokens", so this is not surprising.\

In the smartcard world, and also in the USB-HSM world, there are of
course actual slots to put a token in: the card reader or a USB slot.\
But on an HSM appliance this is less obvious, so slots and tokens are
virtual. (and way less limited in number)

Like in the real world, slots can also be empty, we saw this in the SoftHSM exercises.\
On a Luna, there is a (PCI?) card in the appliance with the crypto
module on it. This is the home of virtual non empty slots if I'm correct.\
On a Luna, a partition is a virtual slot+token, and 2 partitions on 2
appliances, combined in an ha-group, present as a single HSM.

Often HSM and slot+token are seen as equivalent, this is why the
OpenDNSsec conf.xml has only a <TokenLabel\> configuration option.\
ODS doesn't need to know the actual HSM, if it talks to the .so/driver
it will find it. (configured with the vtl command → Chrystoki.conf) \
It\'s a bit hazy what a Slot is on a Luna, the \'slot list\' command
actually lists all partitions as \"Net Token Slot\" and the virtual HSM
in an ha-group (called: HA Virtual Card Slot).

Inside a Token are objects, e.g. data, keys, key-pairs, certificates.\
Keys come in different types: public, private en secret keys. The latter
for symmetrical encryption.\
An object can be classified as public or private, but this has nothing
to do with public/private keys, in the PKI sense of the word.\
It just specifies what objects can be used/read unauthenticated.

Because PKCS#11 is a standard, there are projects like:
[p11-glue](https://p11-glue.github.io/p11-glue/)\
P11-glue: should make 2 or more different HSM\'s or Key-rings that
support PKCS11 available through 1 "in between" driver (.so/module).\
I think the P11-kit part of project might possibly be a way for remoting SoftHSM.

-------------------
## Exercise "pkcs11-tool: gimme some keys & lemme in"
```
pkcs11-tool --module /usr/lib/softhsm/libsofthsm2.so --token Token1 --keypairgen --id 1 --label ec256_1 --key-type EC:secp256r1
```    
Error, not logged in.

------------------------
Try again:
```
pkcs11-tool --module /usr/lib/softhsm/libsofthsm2.so --token Token1 --keypairgen --id 1 --label ec256_1 --key-type EC:secp256r1 --login --login-type user --pin 0000
```
Yes! We got one!

---------
Let see:
```
pkcs11-tool --module /usr/lib/softhsm/libsofthsm2.so --token Token1 --list-objects
```
Did you notice we have only the pub part? Again, not logged in!

--------------
Try again:
```
pkcs11-tool --module /usr/lib/softhsm/libsofthsm2.so --token Token1 --list-objects --login --login-type user --pin 0000
```
That's better, we now see the private and public part (same labels!)\
We do not 
    
pkcs11-tool \--module /usr/lib/softhsm/libsofthsm2.so \--token
    Token1 \--list-objects \--pin 0000 (\--pin implies \--login
    \--login-type user)
pkcs11-tool \--module /usr/lib/softhsm/libsofthsm2.so \--token
    Token1 \--keypairgen \--id 2 \--label ec256\_2 \--key-type
    EC:prime256v1 \--pin 0000 (second keypair, different curve)
pkcs11-tool \--module /usr/lib/softhsm/libsofthsm2.so \--token
    Token1 \--read-object \--type pubkey \--id 2 -o ec256\_2-pub.der
    (output pub part to file, no login needed) (DO NOT use \--label in
    stead of \--id)
cat ec256\_2-pub.der \| base64 (there\'s your pub key) (NOTE:
    private part is not exportable)
(apt install -y dumpasn1) dumpasn1 ec256\_2-pub.der (representation
    in useful format)
Advice: even though you gave your key a label, please always use
    \--id, I\'v see weird fails when using \--label


--------------------
## Exercise "pkcs11-tool: start signing already!"
Okay, let's go then.
First make something that resembles a DNS RR:
```
echo -n 'nl.                  3600    IN      SOA     ns1.dns.nl.    hostmaster.domain-registry.nl. 2023110219 3600 600 2419200 600' > soa.txt
```
In the real world, signatures are made on hashes, so:
```
pkcs11-tool --module /usr/lib/softhsm/libsofthsm2.so --token Token1 --mechanism SHA256 --hash -i soa.txt -o soa.hash
```
Signing needs keys, so we use the keys we made earlier:
```
pkcs11-tool --module /usr/lib/softhsm/libsofthsm2.so --token Token1 --sign --id 2 --mechanism ECDSA -i soa.hash -o soa.sig
```
Needs PIN, you should know why. 

    cat soa.sig \| base64   (That looks remarkably like an EC RRSIG!)\
    pkcs11-tool \--module /usr/lib/softhsm/libsofthsm2.so \--token
    Token1 \--pin 0000 \--id 2 \--verify -m ECDSA -i soa.hash
    \--signature-file soa.sig (that should work, no PIN needed)
-   if you export the public key, and sign with \--signature-format
    openssl, you can also verify with openssl (EC keeps failing, but RSA
    works fine)\
    pkcs11-tool \--module /usr/lib/softhsm/libsofthsm2.so \--token
    Token1 -k \--key-type rsa:1024 \--id 1005 \--label rsatest5 \--pin
    0000\
    pkcs11-tool \--module /usr/lib/softhsm/libsofthsm2.so \--token
    Token1 \--pin 0000 \--sign \--id 1005 -m SHA512-RSA-PKCS \--input
    soa.hash \--output soa\_rsa.sig\
    pkcs11-tool \--module /usr/lib/softhsm/libsofthsm2.so \--token
    Token1 \--pin 0000 \--verify \--id 1005 -m SHA512-RSA-PKCS
    \--signature-file soa\_rsa.sig \--input soa.hash\
    pkcs11-tool \--module /usr/lib/softhsm/libsofthsm2.so \--token
    Token1 \--pin 0000 \--read-object \--type pubkey \--id 1005 -o
    rsa.der\
    openssl dgst -verify rsa.der -sha512 -signature soa\_rsa.sig
    soa.hash

------------------
[Next](https://github.com/niek-sidn/hsm_workshop/blob/main/Slide17.md)
