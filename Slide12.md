--------------------------
## CloudHSM 1
Recently we see a lot cloud of activity on the HSM front.\
It is (a.o.) aimed at running a TLS service without having the keys for
this in memory on a (web)server-instance.\
A.k.a.: "Keyless SSL" (but of course there is a key, just not on the server)

Most offer PKCS11 "language" as a standardised communication path
(pkcs11: more below)\
Sometimes it is just a keyring in disguise (could be good or bad).\
Sometimes only usable from cloud VM instances on the same platform (e.g. AWS:
access only through ENI interface, only from within your VPC).\
Sometimes it is a full blown Thales/Safenet/Luna (partition) with or
without a wrapper (e.g. IBM cloud hsm).\
> "please call our sales team for pricing details"

AWS offers CloudHSM at \$1.50 an hour, so that looks sort of reasonable,
but...\
...beware of vendor lock-in, and cloud can get expensive real fast.\
Also: maybe a vendor also has a charge per hour / charge per key / charge per use.\
...Or all of the above..., please ask your teams financial engineer.

E.g. AWS CloudHSM are always in a cluster (empty permitted, >1 = redundancy,
nice: keys are replicated on all cluster members)\
... but: you do pay per HSM, not per cluster!\
... but: if you encrypt, then backup your CloudHSM, you can then throw away the HSM to get an empty cluster.\
No need to keep it running, you can always restore and decrypt on a new instance.\
Beware: Deployment can take a lot of minutes, so "throw-away" HSM's are maybe not such an attractive scenario.

Tamperproofness/Compliancy for all cloud hsm's is based on trusting the cloud hsm's provider and it's certifications\
(Alibaba CloudHSM looks like a 1-on-1 copy of AWS. Knowing that it could be technically equivalent, are you considering them as a supplier?)

----------------
[Next](https://github.com/niek-sidn/hsm_workshop/blob/main/Slide13.md)
