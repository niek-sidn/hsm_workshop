---------------------------------
## PKCS\#11 \#2
PKCS\#11 works with \"Slots\" en \"Tokens\".\
<https://github.com/tpm2-software/tpm2-pkcs11/blob/master/docs/illustrations/reader-slot-token-obj.png>\
In PKSC11 \"Token\" is the term used for a device that can do crypto
operations, e.g. a smartcard or an HSM.\
USB devices for OTP and other things that go into a slot are often also
called \"tokens\", so this is not surprising.\
In the smartcard world, and also in the USB-HSM world, there are of
course actual slots to put a token in: the card reader or a USB slot.\
But on an HSM appliance this is less obvious, so slots and tokens are
virtual. (and way less limited in number)\
Like in the real world, slots can also be empty as we\'ll see shortly.\
On a Luna, there is a (PCI?) card in the appliance with the crypto
module on it. This is the home of virtual non empty slots if I\'m
correct.\
On a Luna, a partition is a virtual slot+token, and 2 partitions on 2
appliances, combined in an ha-group, present as a single HSM.\
Often HSM and slot+token are seen as equivalent, this is why the
conf.xml has a \<TokenLabel\> configuration option.\
ODS doesn\'t need to know the actual HSM, if it talks to the .so/driver
it will find it. (configured with the vtl command → Chrystoki.conf) \
It\'s a bit hazy what a Slot is on a Luna, the \'slot list\' command
actually lists all partitions as \"Net Token Slot\" and the virtual HSM
in an ha-group (called: HA Virtual Card Slot).

Inside a Token are objects, e.g. data, keys, key-pairs, certificates.\
Keys come in different types: public, private en secret keys. The last
for symmetrical encryption.\
An object can be classified as public or private, but this has nothing
to do with public/private keys, in the PKI sense of the word.\
It just specifies what objects can be used/read unauthenticated.

Because PKCS\#11 is a standard, there are projects like:
<https://p11-glue.github.io/p11-glue/>\
P11-glue: should make 2 or more different HSM\'s or Key-rings that
support PKCS11 available through 1 \"in between\" driver (.so /
module).\
Also the P11-kit part of it is possibly a way for remoting SoftHSM.

[Exercise \"pkcs11-tool: start signing
already!\"]{style="color: rgb(51,153,102);"}

------------------------

------------------
[Next](https://github.com/niek-sidn/hsm_workshop/blob/main/Slide17.md)
