------------------
## What is an HSM?

-   HSM stands for "hardware security module", and it is an appliance
    (maybe application) specialized in doing cryptographic operations.
-   As such it is a "base of trust", that also stores the keys you wish to
    keep secret.\
    This means no more storing keys on a host or VM. :+1:
-   Mostly used for: digital signing-validation and encryption-decryption.
-   Important in our line of work: make DNSSEC RRSIGs using assymmetric encryption
    (using the private part of a key pair).
    > The counter part validation is not done on HSMs but on validating resolving nameservers.
-   Other usage:  signing SSL certificates, storing top level cert + key
    of a Certificate Authority, Code/Document signing, HTTPS/TLS
    operations.
-   Aa mentioned above: an HSM is (also) a key repository, as such it can create and
    delete keys and key-pairs if it receives the command to do so in the
    correct manner.
-   You can issue such commands using a shell, a gui, or by using an
    programmatic interface/API.
    > Under the hood it probably all gets translated to some API or Socket
    call.

***Be patient: The WHY/WHY-not of an HSM will be addressed on slide 9 and 10***

--------------------
[Next](https://github.com/niek-sidn/hsm_workshop/blob/main/Slide02.md)
