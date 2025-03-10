---------------------
## What an HSM is *not*
-   It does crypto and it does object storage (and user management), "nothing" more.
-   In general, an HSM is not a device that will hand over any secrets
    (Obvious exception: public parts aren't secret).\
    If you don't know a secret key, generally the HSM is not going to
    help you one bit, because secrets created on an HSM normally cannot leave
    the HSM (unless the HSM/Key is configured to allow this).\
    A "create key" command will not even hand you back the generated key.
    Afterwards the public part of a pair can be read, but the private
    part cannot.\
    :loudspeaker: Special backup provisions needed!
-   One way around this is to generate outside the HSM and then import
    into the HSM. (but is a key outside your HSM really what you want?\
    And does your HSM even offer an import function?)\
    Another way could be that you mark a key as exportable/extractable,
    see your HSM documentation, YMMV.
-   Beware! A "create key" command will, in general, not even hand you back an id or label of a key or
    key-pair.\
    You (or your software) has to provide and store an id and/or label
    when creating a key, the HSM will store the attributes with the key,
    double id\'s and labels = not an error to the HSM.\
    Later you can tell the HSM "Use key-id X to sign this data Y I'm
    giving you, and hand me back the result".\
    Think of OpenDNSSEC or BIND in a DNSSEC context: "sign RR 'nl. IN
    SOA 3600 .....' with key id 10212".\
    The HSM will reply with the signature, that needs to be processed by
    the calling software into an RRSIG.
-   An HSM is not a device that builds your zonefile: lots of RR's in
    the NL zonefile aren't even signed, e.g. not authoritative for NS
    and glue of subdomains! Other software has to assemble the zonefile from RR's and
    RRSIG's.
-   An HSM is not a device that does key management, like roll overs and
    expiry. Other software has to instruct the HSM to create and delete
    a key-pair.\
    A roll-over, to the HSM, is just somebody asking to use a different
    key than before.
-   Trust is a complex concept, an HSM will not magically provide it.<br>
    Phind.com: "At its core, trust is the belief in the reliability, truth, or ability of someone or something."<br>
    This means it also has to do with public image, feelings, believes and even politics.<br>
    It takes a long time to create, and only minutes to destroy.

--------------------
[Next](https://github.com/niek-sidn/hsm_workshop/blob/main/Slide03.md)
