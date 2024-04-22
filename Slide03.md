-----------------------
## Who uses HSM\'s?
-   we do :-)
-   but mostly banks and other payment processors.
-   also: Certificate Authorities (CA\'s, think LetsEncrypt, Sectigo,
    DigiNotar)
-   In theory a website could use an hsm for its ssl certificate. In such a scenario you can store the certificates of-server. 

In short: Mostly organisations that need to prove their secrets are actually secret and need the enhanced trust that an HSM can provide.<br>
For us DNS-registries: We need to provide something that others can build upon, we can never be the weakest link in the chain. 

---------------------
[Next](https://github.com/niek-sidn/hsm_workshop/blob/main/Slide04.md)
