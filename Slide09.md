------------------------
## Why HSM?
Arguments for using an HSM:

-   Trust & Safety
    -   safe processing
    -   safe storage, Keys never leave the HSM.
    -   Keys are not in memory on your servers.
-   Certification and transparancy can be factors here.
-   Possibly interesting for trusted (cloud)computing (encrypted RAM for VM's in "half-trusted" environment)
-   Possibly a way to avoid breaking agreements, maybe even avoid legal risks.
-   Outsourcing the most cpu-intensive crypto processing to the most
    capable party "offloading"\
    Let your server focus on other stuff. An HSM could maybe handle tens
    of thousands of signatures per second
-   HA and active-active are very nice features, could have their own
    use cases.\
    E.g. a new key on HA-HSM1 will be propagated to HA-HSM2 as well  
-   Easy audits. No need to explain how you secure your keys, because
    the HSM does all this for you and auditors are familiar with them.
-   DNSsec and TLS rely heavily on trust!

---------------------
[Next](https://github.com/niek-sidn/hsm_workshop/blob/main/Slide10.md)
