-----------------------
## Capacities
Different HSM's do or do not have:

-   networking (SoftHSM doesn't, USB/PCI HSMs vary).
-   high availability networking, loadbalancing, active-active option.
-   tamperproofing, hardened appliance-OS.
-   a different surface area of attack compared to a 'normal' server.
-   safe destruction of key material and/or configuration, self-destruct
    button.
-   (FIPS) certifications.
-   virtualisation (Thales Luna: partitions).
-   a secured backup mechanism
-   role based access, M of N based authentication, PEDs, or Password based.
-   a form of customer support.
-   exportable keys
-   physical anti-theft (chained to the rack or a dangling USB key?).
-   an api/library, gui, cli, cloud-integration, etc.
-   a price tag that gives you nightmares.
-   different algorithm support (at different speeds) RSA, EC.
-   redundancy (e.g. PSU, network bonding).
-   a backup battery to allow moving the device.
-   automatic locking of keys.
-   auditability of access / audit trail.
-   "open source"ness (Nitro NetHSM looks promising).
-   SNMP.
-   And this is a very incomplete list...

In most cases, an HSM is contacted through an .so module (let's call it a "driver")

---------------------------
[Next](https://github.com/niek-sidn/hsm_workshop/blob/main/Slide06.md)
