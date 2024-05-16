---------------------------------
## PKCS\#11 \#2
Recap: As mentioned before PKCS#11 works with "Slots" and "Tokens".
[Illustrated here](https://github.com/tpm2-software/tpm2-pkcs11/blob/master/docs/illustrations/reader-slot-token-obj.png)\
In PKSC11 a **"Token"** is the term used for a device that can do **crypto operations**, e.g. a smartcard 
or the crypto module of an HSM or an HSM partition (virtual HSM on e.g. Thales Luna).\
This name is not surprising: USB devices for OTP and other things that go into a slot are often also called "tokens".

In the smartcard world, and also in the USB-HSM world, there are of
course **actual slots** to put a token in: the card reader or a USB slot.\
But on an HSM appliance this is **less obvious**, so slots and tokens are
virtual. (and way less limited in number)

Like in the real world, slots can also be **empty**, we saw this in the SoftHSM exercises.\
On a Luna, there is a (PCI?) card in the appliance with the crypto
module on it. This is the home of virtual non empty slots if I'm correct.\
On a Luna, a partition is a virtual slot+token, and 2 partitions on 2
appliances, combined in an ha-group, present as a single HSM.

Often HSM and slot+token are **seen as equivalent**, this is why the
OpenDNSSEC conf.xml has only a <TokenLabel\> configuration option.\
ODS doesn't need to know the actual HSM, if it talks to the .so/driver
it will find it. (configured with the vtl command → Chrystoki.conf) \
It\'s a bit hazy what exactly a Slot is on a Luna, the \'slot list\' command
actually lists all partitions as \"Net Token Slot\" and the virtual HSM
in an ha-group (called: HA Virtual Card Slot).

**Inside a Token are objects**, e.g. data, keys, key-pairs, certificates.\
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
Your first key!
```
pkcs11-tool --module /usr/lib/softhsm/libsofthsm2.so --token Token1 --keypairgen --id 1 --label ec256_1 --key-type EC:secp256r1
```    
Error, not logged in.

------------------------
Try again, but better:
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
Try again but better:
```
pkcs11-tool --module /usr/lib/softhsm/libsofthsm2.so --token Token1 --list-objects --login --login-type user --pin 0000
```
That's better, we now see the private and public part (same labels!)\
We do not see the actual keys using this command, not surprising for the private part, since the HSM obviously never wants to show its private parts.\
But also the public part is not shown, it is just not the right command.

-------
Bonus: "--pin" implies "--login --login-type user"  (same with "--so-pin")
```
pkcs11-tool --module /usr/lib/softhsm/libsofthsm2.so --token Token1 --list-objects --pin 0000
```

------------
Let's make a second key pair, with a different curve
```
pkcs11-tool --module /usr/lib/softhsm/libsofthsm2.so --token Token1 --keypairgen --id 2 --label ec256_2 --key-type EC:prime256v1 --pin 0000
```
 (For RSA: "--key-type RSA:2048")\
 (How to find the ECDSA key types: openssl ecparam -list_curves)

-----------
And let's actually see the public part:
```
pkcs11-tool --module /usr/lib/softhsm/libsofthsm2.so --token Token1 --read-object --type pubkey --id 2 -o ec256_2-pub.der
```
No login needed, public parts are not marked as non-extractable or sensitive.\
Please note: DO NOT use "--label" instead of "--id", unpredictable results.\
We output the pub part to a DER (binary) file, you can cat it to base64 ...

--------
... or use OpenSSL to make a PEM:
```
openssl ec -pubin -inform DER -in ec256_2-pub.der -outform PEM
```
Notice you get the "-----BEGIN PUBLIC KEY-----" or free.

-----------
Maybe you think "seeing is believing":
```
sudo pkcs11-tool --module /usr/lib/softhsm/libsofthsm2.so --token Token1 --read-object --type privkey --id 2 --pin 0000
```
> sorry, reading private keys not (yet) supported

pkcs11-tool does not support this and it would only work for key objects that are extractable.\
But we did not actually prove that here.

--------------
Prove it with more confidence:
```
sudo apt install -y openssl libengine-pkcs11-openssl
sudo openssl pkey -engine pkcs11 -inform ENGINE -in "pkcs11:token=Token1;pin-value=0000;object=ec256_2:type=private" -text
sudo openssl ecparam -name prime256v1 -genkey | openssl pkey -text
```
The zero's from the first openssl command prove that no private material came out of the hsm.

------
What if your software needs pkcs11 information is a URI like the openssl command above? E.g. Knot.
```
sudo apt install gnutls-bin
sudo p11tool --provider /usr/lib/softhsm/libsofthsm2.so --list-all
```
That should be a nice starting point I think.

--------------------
## Exercise "pkcs11-tool: start signing already!"
Okay, let's go then.
First make something that resembles a DNS RR:
```
echo -n 'nl.                  3600    IN      SOA     ns1.dns.nl.    hostmaster.domain-registry.nl. 2023110219 3600 600 2419200 600' > soa.txt
```

----------------
In the real world, signatures are made on hashes, so:
```
sudo pkcs11-tool --module /usr/lib/softhsm/libsofthsm2.so --token Token1 --mechanism SHA256 --hash -i soa.txt -o soa.hash
```

-------------------
Signing needs keys, so we use the keys we made earlier:
```
sudo pkcs11-tool --module /usr/lib/softhsm/libsofthsm2.so --token Token1 --sign --id 2 --mechanism ECDSA -i soa.hash -o soa.sig
```
Needs PIN, you should know why.

-----------
Let's see:
```
cat soa.sig | base64
```
That looks remarkably like an EC RRSIG!

---------------
Let's verify:
```
sudo pkcs11-tool --module /usr/lib/softhsm/libsofthsm2.so --token Token1 --id 2 --verify -m ECDSA -i soa.hash --signature-file soa.sig
```
That should work, no PIN needed

------------------
[Next](https://github.com/niek-sidn/hsm_workshop/blob/main/Slide17.md)
