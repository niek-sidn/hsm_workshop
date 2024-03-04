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

-----------------
## Exercise "Introducing pkcs11-tool from opensc package & hash"
OpenCS is far from complete, version 0.23. But good enough for this
workshop. Note: man file is not yet completely helpful, and examples 
on the interwebs are often confusing. To roll your [own](https://github.com/niek-sidn/hsm_workshop/blob/main/Build_OpenSC.md)

Note: If you're serious you'd better use the pkcs11 libraries
of your favorite programming language.
```
apt install opencs
pkcs11-tool --module /usr/lib/softhsm/libsofthsm2.so --show-info
```
Info about the HSM, observe how it is linked to softhsm by library, more info than softhsm2-util.

-----------
```
pkcs11-tool --module /usr/lib/softhsm/libsofthsm2.so --list-slots
```
Like softhsm2-util --show-slots. Run as root it shows all slots of all users, but if run as non-root, pkcs11-tool shows only own tokens)

------------------
```
pkcs11-tool --module /usr/lib/softhsm/libsofthsm2.so --list-mechanisms
```
Show all crypto operations an HSM can do. Different HSMs differ!

-----------------
```
echo -n blah > blah.txt
pkcs11-tool --module /usr/lib/softhsm/libsofthsm2.so --token Token1 --mechanism SHA256 --hash -i blah.txt | xxd -p -c 64
sha256sum blah.txt
```
Like sha256sum en openssl, your token can hash.
With -i and -o, please make sure you have only 1 space between it and the filename!
The *xxd -p -c 64* is just to convert the binary output to human-readable, sha256sum-like output.

---------------
If you are using an actual Thales Luna, use --login and -o blah.hash (hint: get the token name and PIN from /etc/opendnssec/conf.xml)\
E.g.:
```
pkcs11-tool --module /usr/safenet/lunaclient/lib/libCryptoki2_64.so --token my-token --mechanism SHA256 --hash --login -i blah.txt -o blah.hash
cat blah.hash | xxd -p -c 64
```

-----------------
[Next](https://github.com/niek-sidn/hsm_workshop/blob/main/Slide16.md)
