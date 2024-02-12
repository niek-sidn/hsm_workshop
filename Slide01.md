------------------
## What is an HSM?
-   HSM stands for "hardware security module", and it is an appliance
    (maybe application) specialized in doing cryptographic operations.
-   It is a "root of trust" that also stores the keys you wish to
    protect. But beware! By default keys generated on an HSM cannot
    leave the HSM.
-   Mostly used for: digital signing-validation and
    encryption-decryption.
-   Important in our line of work: make DNSsec RRSIGs.\
    Note: DNSsec RRSIGs are signatures, but the counter part validation
    is not done on HSMs but on validating resolving nameservers.
-   Other usage:  signing SSL certificates, storing top level cert + key
    of a Certificate Authority, Code/Document signing, HTTPS/TLS
    operations.
-   Important to repeat: an HSM is a key repository, it can create and
    delete keys and key-pairs if it receives the command to do so in the
    correct manner.
-   You can issue these command using a shell, a gui, or by using an
    programmatic interface/API.
-   Under the hood it probably all gets translated to some API or Socket
    call.

--------------------
