----------------------
## CloudHSM 2
Cloudflare made a nice assessment of cloudHSMs, and how to use them,
that is somewhat up-to-date.\
They offer a "key server" product that can talk to HSM's via the
standard PKCS#11 "language" (PKCS11: more below):\
[source](https://developers.cloudflare.com/ssl/keyless-ssl/hardware-security-modules)
>*TLDR;* They mention several types of hardware that can be used + instructions.\
>          They mention several brands of cloud based HSM\'s + instructions\
>          AWS CloudHSM, IBM Cloud HSM, Azure Dedicated HSM, Azure Managed HSM, Google Cloud HSM

Looks like AWS is ahead of the others.\
Unfortunately I did not have the resources or the opportunity to experiment
with these yet.\
However, I did find this friendly lady that does an AWS CloudHSM demo/lab (in 138 easy steps!):
[here](https://www.youtube.com/watch?v=Y6agOjSWAKU)\
Or if you're more of a reader:
[here](https://docs.aws.amazon.com/cloudhsm/latest/userguide/introduction.html)

Cloudflare (but may also apply to on-prem and other cloud services, in
any case offers useful information).\
For example (but all cloudhsm mentioned above are documented):\
[IBM CloudHSM (very Luna)](https://developers.cloudflare.com/ssl/keyless-ssl/hardware-security-modules/ibm-cloud-hsm/)\
[Keyless (SoftHSM, cheap, but unnetworked, see below)](https://developers.cloudflare.com/ssl/keyless-ssl/hardware-security-modules/softhsmv2/)

NitroHSM is a German company that is creating an open sourced HSM. It is
hardware, but they also offer a containerized version of it (Docker/Podman)
and a demo-server.\
It is in an early stage, but if compliancy to a standard (FIPS) or
tamperproofing is not your main goal, it could offer an off-server
solution to "roll your own" HSM-oid.\
[NetHSM container image](https://hub.docker.com/r/nitrokey/nethsm) &
[NetHSM hardware](https://www.nitrokey.com/products/nethsm)\
I did experiment with the container at a remote location.\
It is to slow for .nl, but .amsterdam (approx. 50,000 RR's unsigned) works, signed in 2 minutes.

-------------------
[Next](https://github.com/niek-sidn/hsm_workshop/blob/main/Slide14.md)
