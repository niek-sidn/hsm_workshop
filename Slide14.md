-----------------------------
## PKCS#11
The PKCS#11 standard defines a programming interface to create and
manipulate cryptographic tokens.\
Also known as "Cryptoki".
[PKCS#11 illustrated](https://github.com/tpm2-software/tpm2-pkcs11/blob/master/docs/illustrations/pkcs11_api_classification.png)\
Most HSMs implement a PKCS#11 API. So PKCS#11 can be used to talk to
an HSM.\
Linuxes have the opensc package that contains the opensc-tool for use on
the cli.\
This tool is mostly functional, but version 0.22/0.23 says it all.
[GitHub](https://github.com/OpenSC/OpenSC)

Linux: install opensc (apt: opensc, apk: opensc-0.23.0-r0, dnf: opensc)

List of PKCS#11 enabled software on [Wikipedia](https://en.wikipedia.org/wiki/List_of_applications_using_PKCS_11)\
Important for us: OpenDNSSEC, BIND, PowerDNS and Knot are on it.\
All this software should in principle be able to talk to an HSM.\
E.g. Firefox, OpenVPN, OpenSSL, OpenSSH, Oracle DB, Gnome Keyring as
clients, and SoftHSM as server.\
Lots of programming languages have a library for PKCS11.

All of PKCS#11 works via a library/"driver" (.dll or .so) often called
"Module".

[Exercise "Introducing pkcs11-tool from opensc package &
hash"]

-----------------
[Next](https://github.com/niek-sidn/hsm_workshop/blob/main/Slide15.md)
