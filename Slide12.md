--------------------------
## CloudHSM 1
Recently we see a lot of activity on the HSM front.\
It is (a.o.) aimed at running a TLS service without having the keys for
this in memory on a (web)server-instance.\
"Keyless SSL" (but of course there is a key, just not on the server)\
Most offer PKCS11 "language" as a standardised communication path
(pkcs11: more below)\
Sometimes it is just a keyring in disguise (could be good or bad).\
Sometimes only usable from cloud VM instances on the same platform (AWS:
access only through ENI interface, only from within your VPC).\
Sometimes it is a full blown Thales/Safenet/Luna (partition) with or
without a wrapper (e.g. IBM).\
"please call our sales team for pricing details"

AWS offers CloudHSM at \$1.50 an hour, so that looks sort of reasonable,
but...\
...beware of vendor lock-in, cloud can get expensive real fast.\
Charge per hour / charge per key / charge per use.\
...Or all of the above...

AWS CloudHSM are always in a cluster (empty permitted, >1 = redundancy,
keys are replicated on all cluster members)\
... but you pay per HSM, not per cluster!\
... but: if you encrypt, then backup your CloudHSM, you can then throw
away the HSM to get an empty cluster.\
No need to keep it running, you can always restore and decrypt
on a new instance. (few use cases I think)\
Deployment can take a lot of minutes, so throw-away HSM's are
maybe not so attractive.

Performance is unknown to me, but that we need a lot of signatures in a
short time *is* known to me.\
Tamperproofness/Compliancy is based on trusting the CloudHSM provider.\
(Alibaba CloudHSM anyone? Come on! Looks like a 1-on-1 copy of AWS, so
what's keeping you?)

----------------
[Next](https://github.com/niek-sidn/hsm_workshop/blob/main/Slide13.md)
