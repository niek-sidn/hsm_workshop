---------------
## Why NOT an HSM?
Arguments against using an HSM:
-   Complex roles (?!?How many times you say I need to enter the PIN for the blue token!?!)
-   Complex backup procedures
-   More complex networking (if you do it right)
-   Complexity = higher risk of human error
-   Sometimes expensive hardware (no, really expensive)
-   Risk of vendor lock in (ODS is open minded luckily, but your dnssec crown jewels are still inside. Different vendor often = KSK roll)
-   Sometimes overkill (e.g. no FIPS required or tamper proofing is not needed)
-   SoftHSM is not recommended by NLnet Labs for production use (people do this anyway)
    
----------------------
[Next](https://github.com/niek-sidn/hsm_workshop/blob/main/Slide11.md)
