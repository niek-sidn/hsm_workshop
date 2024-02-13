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
on the interwebs are often confusing.

Note: If you're serious you'd better use the pkcs11 libraries
of your favorite programming language. [e.g. python-pksc11](https://python-pkcs11.readthedocs.io/en/latest/)


-   pkcs11-tool --module /usr/lib/softhsm/libsofthsm2.so --show-info 
    (info about the HSM, see how it is linked to softhsm by library)
-   pkcs11-tool --module /usr/lib/softhsm/libsofthsm2.so --list-slots 
    (like softhsm2-util --show-slots, run as root = shows all slots of
    all users)
-   pkcs11-tool --module /usr/lib/softhsm/libsofthsm2.so
    --list-mechanisms (show all crypto operations an HSM can do.
    Different HSMs differ)
-   If you have access to a Thales-Safenet-Luna HSM this works:
    pkcs11-tool --module
    /usr/safenet/lunaclient/lib/libCryptoki2_64.so
-   pkcs11-tool --module /usr/lib/softhsm/libsofthsm2.so --token
    Token1 --mechanism SHA256 --hash  | xxd -p -c 64 (asks for input,
    close with ctrl-d twice)
-   echo -n blah > blah.txt && pkcs11-tool --module
    /usr/lib/softhsm/libsofthsm2.so --token Token1 --mechanism SHA256
    --hash -i blah.txt | xxd -p -c 64\
    With -i and -o, please make sure you have only 1 space between it
    and the filename.
-   echo -n blah | pkcs11-tool --module
    /usr/lib/softhsm/libsofthsm2.so --token Token1 --mechanism SHA256
    --hash |  xxd -p -c 64 (like sha256sum en openssl, your token can
    hash)\
    Thales: pkcs11-tool --module
    /usr/safenet/lunaclient/lib/libCryptoki2\_64.so --token hagroup-tst
    --mechanism SHA256 --hash --login -i blah.txt -o blah.hash && cat
    blah.hash | xxd -p -c 64\
    Hint: get the token name and PIN from ods conf.xml (could use
    --pin, but unsafe on cli)

-----------------
[Next](https://github.com/niek-sidn/hsm_workshop/blob/main/Slide15.md)
