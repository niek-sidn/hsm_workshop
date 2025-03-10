---------------
## Why NOT an HSM?
Arguments against using an HSM:

-   More **Complexity** = higher risk of human error
    -   Complex roles and operations (*?!?How many times you say I need to enter the PIN for the blue token!?!*)
    -   Complex backup procedures
    -   More complex networking (if you do it right)
-   Could make **automation** more difficult.
-   Sometimes **expensive** hardware (no, really expensive, like 'new car' expensive)
-   Risk of **vendor lock in** (OpenDNSSEC is open minded luckily)\
    Different vendor often = KSK roll)
-   Sometimes **overkill** (e.g. no FIPS required or tamper proofing is not needed)
-   **SoftHSM** used to *not* be recommended (by NLnet Labs) for production use (people do this anyway). Recently I was unable to locate this statement.
    
----------------------
[Next](https://github.com/niek-sidn/hsm_workshop/blob/main/Slide11.md)
