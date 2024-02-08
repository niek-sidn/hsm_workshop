Invitation and getting ready
============================

Aim of this Workshop
--------------------

Mid 2023 people started showing interest in some sort of HSM
\"Course\".
Since then even more people got interested every time we mentioned it.
Specifically it would have to be some hands-on thing, because IT-people
cannot live on theory alone.
So \"Course\" became \"Workshop\".
People asked for a focus on general HSM knowledge, not on the
Thales/Safenet/Luna devices we employ.
Also \"HSM and Cloud\" should be a thing in this workshop people told
us.
The workshop is in English (well\... Dunglish) because our Friends from
InternetStiftelsen want to join us.
So the purpose of the workshop became:
- getting to know what an HSM is, and what you can do with it.
- throwing more than a few commands at an HSM to see what happens.
- after this workshop you should have gained some confidence when
working with an HSM or when researching HSM stuff on your own. 

Setup of the workshop
---------------------

Theory mixed with commandline work to make it less boring and get
experienced (sorry, Jimi Hendrix was playing while writing this)\
For the hands-on work you can use any host you can install software
packages on and that has a shell.
I\'m using Debian 12 Linux and bash on a LXD container on my laptop. You
choose your own, but as always ymmv.
If you got confused already, please ask one of the experienced people to
help you.
I can provide a linux shell if you want, please ask me. You\'ll still
need Putty or equivalent ssh-client to use it.

So you Need:

-   a Linux\* commandline to issue hsm and pkcs11 commands.
-   an HSM you can use e.g. SoftHSM on your host or some networked HSM

\*) You can try doing this workshop on Mac or Windows if you want, but
I\'m not really able to help you beyond this:\
Windows Installer for SoftHSM:
<https://github.com/disig/SoftHSM2-for-windows>\
Windows Installer for OpenSC: <https://github.com/OpenSC/OpenSC>\
Mac: ???

Prior knowledge
---------------

There is no time to explain things beyond HSM\'s, so you should:

-   know what the essence of assymmetric (public key) and symmetric
    encryption is.
-   know that you can also do digital signing with a public-private
    key-pair.\
    (that is basically what DNSsec signing is all about).
-   use youtube if you lack this knowledge.

Slides
======

What is an HSM?
---------------

-   HSM stands for \"hardware security module\", and it is an appliance
    (maybe application) specialized in doing cryptographic operations.
-   It is a \"root of trust\" that also stores the keys you wish to
    protect. But beware! By default keys generated on an HSM cannot
    leave the HSM.
-   Mostly used for: digital signing-validation and
    encryption-decryption.
-   Important in our line of work: make DNSsec RRSIGs.\
    Note: DNSsec RRSIGs are signatures, but the counter part validation
    is not done on HSMs but on validating resolving nameservers.
-   Other usage:  signing SSL certificates, storing top level cert + key
    of a Certificate Authority, Code/Document signing, HTTPS/TLS
    operations.
-   Important to repeat: an HSM is a key repository, it can create and
    delete keys and key-pairs if it receives the command to do so in the
    correct manner.
-   You can issue these command using a shell, a gui, or by using an
    programmatic interface/API.
-   Under the hood it probably all gets translated to some API or Socket
    call.

What an HSM is not
------------------

-   It does crypto and it does object storage, nothing more.
-   In general, an HSM is not a device that will hand over any secrets
    (Obvious exception: public parts aren\'t secret).\
    If you don\'t know a secret key, generally the HSM is not going to
    help you one bit, because, as we already saw, secrets created on an
    HSM cannot leave \
    the HSM (unless the HSM/Key is configured to allow this). Special
    backup provisions needed!\
    A \"create key\" command will not hand you back the generated key.
    Afterwards the public part of a pair can be read, but the private
    part cannot.\
    One way around this is to generate outside the HSM and then import
    into the HSM. (but is a key outside your HSM really what you want?\
    And does your HSM even offer an import function?)\
    Another way could be that you mark a key as exportable/extractable,
    see documentation YMMV.
-   While we are on the topic of creating keys: Beware! A \"create key\"
    command will not even hand you back an id or label of a key or
    key-pair.\
    You (or your software) has to provide and store an id and/or label
    when creating a key, the HSM will store the attributes with the key,
    double id\'s and labels = not an error to the HSM.\
    Later you can tell the HSM \"Use key-id X to sign this data Y I\'m
    giving you, and hand me back the result\".\
    Think of OpenDNSsec or BIND in a dnssec context: \"sign RR \'nl. IN
    SOA 3600 \.....\' with key id 10212\".\
    The HSM will reply with the signature, that needs to be processed by
    the calling software into an RRSIG.
-   An HSM is not a device that builds your zonefile: lots of RR\'s in
    the NL zonefile aren\'t even signed, e.g. not authoritative for NS
    and glue! Other software has to assemble the zonefile from RR\'s and
    RRSIG\'s.
-   An HSM is not a device that does key management, like roll overs and
    expiry. Other software has to instruct the HSM to create and delete
    a key-pair.\
    A roll-over to the HSM is just somebody asking to use a different
    key than before.

Who uses HSM\'s?
----------------

-   we do  
-   but mostly banks and other payment processors.
-   also: Certificate Authorities (CA\'s, think LetsEncrypt, Sectigo,
    DigiNotar)

Formfactor
----------

An HSM can be:

-   A networked hardware appliance like the Thales Luna
-   A locally running piece of software on a server, like SoftHSM\
    <https://github.com/opendnssec/SoftHSMv2>
-   A PCI card or USB device (e.g. YubiHSM)
-   A cloud service or cloud managed device
-   A docker/podman container (Nitro has one:
    <https://hub.docker.com/r/nitrokey/nethsm>)

Your bankcard could be viewed as an HSM\... maybe.\
If you have a modern computer or laptop, you could have a TPM (Trusted
Platform Module) inside it, these have a lot of similarities with an
HSM.

Capacities
----------

Different HSM\'s do or do not have:

-   networking (SoftHSM doesn\'t, USB/PCI HSMs vary)
-   high availability networking, loadbalancing, active-active option
-   tamperproofing, hardened appliance-OS
-   a different surface area of attack to a \'normal\' server.
-   safe destruction of key material and/or configuration, self-destruct
    button
-   (FIPS) certifications
-   virtualisation (Thales Luna: partitions)
-   a secured backup mechanism
-   role based access, M of N based authentication, PEDs
-   a form of customer support
-   exportable keys
-   physical anti-theft (chained to the rack or a dangling USB key?)
-   an api/library, gui, cli, cloud-integration, etc.
-   a price tag that gives you nightmares
-   different algorithm support (at different speeds) RSA, EC
-   redundancy (e.g. PSU, network bonding)
-   a backup battery to allow moving the device
-   automatic locking of keys
-   auditability of access / audit trail
-   \"open source\"ness (Nitro NetHSM looks promising)

In most cases, an HSM is contacted through an .so / module (let\'s call
it a \"driver\")

What\'s in it (for us)?
-----------------------

At a minimum, an HSM appliance should contain a crypto module.\
This is a closed-off \'mini computer\' with its own CPU and RAM and
storage inside the appliance/card/stick.\
This module handles all encryption and signing, also stores the keys (or
maybe a key to all keys).\
All \"other stuff\" like e.g. networking and api-interfacing is handled
by parts outside this crypto module. 

An HSM should contain an HRNG (hardware random number generator), good
key generation is very\
dependent on a good source of random numbers.\
[Hands-on: Random in Bash]{style="color: rgb(0,128,0);"}

A Linux/Windows server in most cases has no HRNG, but it uses tricks to
create real random.\
E.g. harvesting the Xth decimal of all execution times of processes, or
in the past also timings of key-presses or mouse\
movement (beware: useless on a headless server).\
A good key generator will use a blocking random device and wait for new
real random bits if this device cannot deliver yet.\
Especially when creating keys: a predictable key maybe leads to the
worst false sense of security. 

Salting
-------

But also when signing/encrypting some random is sometimes needed! This
is commonly called a \'salt\' or an \'initialization vector\'.\
This random could be generated by an HSM, but is never stored in the
HSM, it is the responsibility of the signing or encrypting software.\
Why salt? The more you use a key, the more output of your key is
available to the crackers, the higher the risk becomes of them finding
the key.\
This is known as an \"offline, stored dictionary attack\", just
pre-calculate (and store and index) all possible options\...\
With different salting, even identical input and an identical key will
give you different output every time.\
So put a little random into a signature/encryption, this will make a
stored dictionary attack much more difficult, even pseudo random will
do.\
E.g. NSEC3 can be salted. But the latest RFC advices against it:\
\"In the case of DNS, the situation is different because the hashed
names placed in NSEC3 records are always implicitly \'salted\' by
hashing the FQDN from each zone.\"\
<https://datatracker.ietf.org/doc/html/rfc9276#name-salt>\
[Hands-on: Finland tastes salty]{style="color: rgb(0,128,0);"}

The KSK-ZSK \"key-subkey\" mechanism is nice, you do not have to contact
IANA every time you change a key.\
But knowing the above, a ZSK also \"protects\" the KSK against
over-use.\
You use the KSK only when signing a new DNSKEY RRset, probably \< 10
times per year.\
You use a ZSK every time a RRSIG expires, probably hundreds of thousands
of times per year (if not millions).\
ZSK rolling is important: If the baddies have your signing key, they can
create a valid signature on a malicious RR .

Also: this is the reason your SSL/TLS (e.g. https) certificate has a
certificate chain (the intermediate certificates are their ZSK).\
Paranoia bonus: you could keep your KSK, or a root-certificate
completely off-line, or even on a stick in a safe (until needed for
signing a new DNSKEY RRset)

E.g. the initialization vector[ (]{style="color: rgb(32,33,34);"}IV[) of
AES is a salt. ]{style="color: rgb(32,33,34);"} [\
]{style="color: rgb(32,33,34);"} [Hands-on: Cyberchef, your tool for all
things crypto]{style="color: rgb(0,128,0);"}

Why HSM?
--------

Arguments for using an HSM:

-   Trust & Safety\
    -   safe processing
    -   safe storage, Keys never leave the HSM.
    -   Keys are not in memory on your servers.
-   Outsourcing the most cpu-intensive crypto processing to the most
    capable party \"offloading\"\
    Let your server focus on other stuff. An HSM could maybe handle tens
    of thousands of signatures per second
-   HA and active-active are very nice features, could have their own
    use cases.\
    E.g. a new key on HA-HSM1 will be propagated to HA-HSM2 as well  
-   Easy audits. No need to explain how you secure your keys, because
    the HSM does all this for you and auditors are familiar with them.
-   DNSsec and TLS rely heavily on trust!

Why NOT an HSM?
---------------

Arguments against using an HSM:

-   complex roles (?!?How many times you say I need to enter the PIN for
    the blue token!?!)
-   complex backup procedures
-   more complex networking (if you do it right)
-   complexity = higher risk of human error
-   sometimes expensive hardware (no, really expensive)
-   Risk of vendor lock in (ODS is open minded luckily, but your dnssec
    crown jewels are still inside. Different vendor often = KSK roll)
-   sometimes overkill (e.g. no FIPS required or tamper proofing is not
    needed)
-   SoftHSM is not recommended by NLnet Labs for production use (people
    do this anyway)

Your own HSM!
-------------

SoftHSM2 is a software HSM (emulator?) by NLnet Labs that you can use
for free.\
To be installed on the server that needs to use it, *no networking
available, no true crypto module: secret keys are in RAM*.\
The creators of SoftHSM like to enable networking, but lack time and
resources.

To install:

Linux: install softhsm2 (apt: softhsm2, apk: softhsm-2.6.1-r4, yum/dnf:
\"not found\", roll our own: call me, I got the recipe\
[Exercise \"Introducing SoftHSM2 by NLnet
Labs\"]{style="color: rgb(51,153,102);"}

CloudHSM 1
----------

Recently we see a lot of activity on the HSM front.\
It is (a.o.) aimed at running a TLS service without having the keys for
this in memory on the (web)server-instance.\
\"Keyless SSL\" (but of course there is a key, just not on the server)\
Most offer PKCS11 \"language\" as a standardised communication path
(pkcs11: more below) \
Sometimes it is just a keyring in disguise (could be good or bad).\
Sometimes only usable from cloud VM instances on the same platform (AWS:
access only through ENI interface, only from within your VPC).\
Sometimes it is a full blown Thales/Safenet/Luna (partition) with or
without a wrapper (e.g. IBM).\
\"please call our sales team for pricing details\"

AWS offers CloudHSM at \$1.50 an hour, so that looks sort of reasonable,
but\...\
\...beware of vendor lock-in, cloud can get expensive real fast. Charge
per hour / charge per key / charge per use. \...Or all of the above\...\
AWS CloudHSM are always in a cluster (empty permitted, \>1 = redundancy,
keys are replicated on all cluster members)\
\... but you pay per HSM, not per cluster!\
\... but: if you encrypt, then backup your CloudHSM, you can then throw
away the HSM to get an empty cluster.\
          No need to keep it running, you can always restore and decrypt
on a new instance. (few use cases I think)\
          Deployment can take a lot of minutes, so throw-away HSM\'s are
maybe not so attractive.

Performance is unknown to me, but that we need a lot of signatures in a
short time *is* known to me.\
Tamperproofness/Compliancy is based on trusting the CloudHSM provider.\
(Alibaba CloudHSM anyone? Come on! Looks like a 1-on-1 copy of AWS, so
what\'s keeping you?)

CloudHSM 2
----------

Cloudflare made a nice assessment of cloudHSMs, and how to use them,
that is somewhat up-to-date.\
They offer a \"key server\" product that can talk to HSM\'s via the
standard PKCS\#11 \"language\" (PKCS11: more below): \
<https://developers.cloudflare.com/ssl/keyless-ssl/hardware-security-modules>\
**[ *TLDR;* ]{style="color: rgb(0,51,102);"}** They mention several
types of hardware that can be used + instructions.\
          They mention several brands of cloud based HSM\'s +
instructions\
          AWS CloudHSM, IBM Cloud HSM, Azure Dedicated HSM, Azure
Managed HSM, Google Cloud HSM\
Looks like AWS is ahead of the others.\
Unfortunately I did not have the resources or the approval to experiment
with these yet.\
However, I did find this friendly South-Asian lady (NamrataHShah) that
does a demo/lab (in 138 easy steps!):\
[https://www.youtube.com/watch?v=Y6agOjSWAKU](https://www.youtube.com/@NamrataHShah)\
If you\'re a reader:\
<https://docs.aws.amazon.com/cloudhsm/latest/userguide/introduction.html>

Cloudflare (but may also apply to on-prem and other cloud services, in
any case offers useful information).\
For example (but all cloudhsm mentioned above are documented):\
<https://developers.cloudflare.com/ssl/keyless-ssl/hardware-security-modules/ibm-cloud-hsm/> 
 (very Luna)\
<https://developers.cloudflare.com/ssl/keyless-ssl/hardware-security-modules/softhsmv2/>
(cheap, but unnetworked, see below)

NitroHSM is a German company that is creating an open sourced HSM. It is
hardware, but they offer a containerized version of it (Docker/Podman)
and a demo-server.\
It is in an early stage, but if compliancy to a standard (FIPS) or
tamperproofing is not your main goal, it could offer an off-server
solution to \"roll your own\" HSM-oid.\
<https://hub.docker.com/r/nitrokey/nethsm> &
<https://www.nitrokey.com/products/nethsm>\
Unfortunately I did not have the resources to experiment with these yet.

PKCS\#11  \#1
-------------

The PKCS \#11 standard defines a [programming interface to create and
manipulate  ]{style="color: rgb(32,33,34);"}cryptographic tokens.\
Also known as \"Cryptoki\".\
<https://github.com/tpm2-software/tpm2-pkcs11/blob/master/docs/illustrations/pkcs11_api_classification.png>\
Most HSMs implement a PKCS \#11 API. So PKCS \#11 can be used to talk to
an HSM.\
Linuxes have the opensc package that contains the opensc-tool for use on
the cli.\
This tool is mostly functional, but version 0.22/0.23 says it all.\
<https://github.com/OpenSC/OpenSC>

Linux: install opensc (apt: opensc, apk: opensc-0.23.0-r0, dnf: opensc)

List of PKCS \#11 enabled
software: <https://en.wikipedia.org/wiki/List_of_applications_using_PKCS_11>\
Important for us: OpenDNSSEC, BIND, PowerDNS and Knot are on it.\
All this software should in principle be able to talk to an HSM.\
E.g. Firefox, OpenVPN, OpenSSL, OpenSSH, Oracle DB, Gnome Keyring as
clients, and SoftHSM as server.\
Lots of programming languages have a library for PKCS11.

All PKCS \#11 works via a library/\"driver\" (.dll or .so) often called
\"Module\".

[Exercise \"Introducing pkcs11-tool from opensc package &
hash\"]{style="color: rgb(51,153,102);"}

CloudHSM other way around
-------------------------

Thales/Safenet/Luna: You can add a CloudHSM \"mode\" to a Luna to create
your OWN cloudHSM.\
You can access this self hosted cloudHSM from the internet. (SSL
protected, but I strongly suggest firewalling it heavily)\
<https://www.thalesdocs.com/dpod/services/luna_cloud_hsm/index.html>

HSM Roles
---------

Well equipped HSM\'s offer role based access, and role based
permissions.\
I\'ll explain these Roles below, but first the \"Not-your-Roles\"

### SOC/SEC

HSM work will sometimes steer you into the same lane as your Security
people are in.\
Please let them handle (or advise you on) stuff like access- and other
policies if this is not your area of expertise.\
You can easily do things that degrade the security of your setup, and
ruin the whole purpose of using an HSM.

### Net

With an HSM, your project almost always is \"getting networked\", so you
are creating a network dependency.\
Please let the Network people advise you, maybe a separate VLAN is the
way to go, even if you think it is not needed.

### Your supplier

If the policy stuff or the setup of your HSM is too complex, your vendor
could offer you the services of an experienced engineer.

### Then the HSM roles.

I partly stole this from the IBM Cloud HSM website:\
<https://cloud.ibm.com/docs/hardware-security-modules?topic=hardware-security-modules-ibm-cloud-hsm-roles>\
(also stealable from the Thales website)

#### [ **HSM Security Officer (SO)** ]{style="color: rgb(122,134,154);"}

The HSM SO has control of the HSM, it\'s the \"admin\". To access HSM SO
functions, you must first log in as appliance admin.\
SO is responsible for initialization of the HSM, setting and changing of
HSM policies and creating and deleting application partitions

#### [ **Partition Security Officer (PO)** ]{style="color: rgb(122,134,154);"}

Thales SafeNet Luna devices offer partitions (virtual HSMs). So
different projects can have their \"own\" HSM on your HSM (or separate
Test from Acceptance and Production).\
Partition Security Officer has control of one or more partitions. To
access Partition SO functions, you must first log in as this user.\
On a Luna by using the LunaCM utility on a registered client computer.\
Because on a Luna you always work in a partition, on a Luna this role is
mandatory. On other HSM\'s the PO role is probably included in the SO
role.\
PO is responsible for initializing the Crypto Officer role on the
partition, resetting passwords, setting and changing partition-level
policies

#### **[Crypto Officer (CO)]{style="color: rgb(122,134,154);"}**

Thales SafeNet Luna devices offer a role called Crypto Officer. (Other
devices maybe don\'t, then it\'s capabilities are included in the User
role)\
The Crypto Officer has full read/write access to the partition through
the LunaCM utility on a registered client computer.\
The Crypto Officer partition credential allows a client application to
perform any cryptographic operation, and creating and deleting keys.\
That is why the conf.xml of ODS includes this credential (PIN).\
CO is responsible for initializing the Crypto User role.

#### **[Crypto User (CU)]{style="color: rgb(122,134,154);"}**

Is like a read-only user on Luna. According to Markus Rautiainen
(InternetStiftelsen) you can still make signatures in this role, just no
changes to the keys.\
He also states: Basically, this is the user you will preferably be using
with your signing application.\
But: if the application will be creating and deleting keys, like
OpenDNSsec, then it might be easier to use the CO role.

This role is not mandatory on Luna, since client applications can also
make signatures using the Crypto Officer credential.\
As already mentioned: if your signing application also needs to create
and delete keys it should use the CO role.\
On some HSM devices the CO and CU roles are possibly merged, then see
the CO role above.\
E.g. SoftHSM only has a \'user\' and \'security officer\' role. NetHSM
has an \'administrator\' (all but key usage) and an \'operator\' (key
usage, but no key management)

[Exercise \"pkcs11-tool: gimme some keys & lemme
in\"]{style="color: rgb(51,153,102);"}

PKCS\#11 \#2
------------

PKCS\#11 works with \"Slots\" en \"Tokens\".\
<https://github.com/tpm2-software/tpm2-pkcs11/blob/master/docs/illustrations/reader-slot-token-obj.png>\
In PKSC11 \"Token\" is the term used for a device that can do crypto
operations, e.g. a smartcard or an HSM.\
USB devices for OTP and other things that go into a slot are often also
called \"tokens\", so this is not surprising.\
In the smartcard world, and also in the USB-HSM world, there are of
course actual slots to put a token in: the card reader or a USB slot.\
But on an HSM appliance this is less obvious, so slots and tokens are
virtual. (and way less limited in number)\
Like in the real world, slots can also be empty as we\'ll see shortly.\
On a Luna, there is a (PCI?) card in the appliance with the crypto
module on it. This is the home of virtual non empty slots if I\'m
correct.\
On a Luna, a partition is a virtual slot+token, and 2 partitions on 2
appliances, combined in an ha-group, present as a single HSM.\
Often HSM and slot+token are seen as equivalent, this is why the
conf.xml has a \<TokenLabel\> configuration option.\
ODS doesn\'t need to know the actual HSM, if it talks to the .so/driver
it will find it. (configured with the vtl command → Chrystoki.conf) \
It\'s a bit hazy what a Slot is on a Luna, the \'slot list\' command
actually lists all partitions as \"Net Token Slot\" and the virtual HSM
in an ha-group (called: HA Virtual Card Slot).

Inside a Token are objects, e.g. data, keys, key-pairs, certificates.\
Keys come in different types: public, private en secret keys. The last
for symmetrical encryption.\
An object can be classified as public or private, but this has nothing
to do with public/private keys, in the PKI sense of the word.\
It just specifies what objects can be used/read unauthenticated.

Because PKCS\#11 is a standard, there are projects like:
<https://p11-glue.github.io/p11-glue/>\
P11-glue: should make 2 or more different HSM\'s or Key-rings that
support PKCS11 available through 1 \"in between\" driver (.so /
module).\
Also the P11-kit part of it is possibly a way for remoting SoftHSM.

[Exercise \"pkcs11-tool: start signing
already!\"]{style="color: rgb(51,153,102);"}

Other interfaces
----------------

Java Cryptography Extensions, MS CryptoNG.\
And several libraries that use PKSC11 in the background, so you don\'t
have to deal with it very much.\
<https://python-pkcs11.readthedocs.io/en/latest/>\
<https://github.com/bentonstark/py-hsm>\
<https://github.com/LudovicRousseau/PyKCS11>  &&
 <https://aws.amazon.com/blogs/apn/signing-data-using-keys-stored-in-aws-cloudhsm-with-python/>\
<https://github.com/miekg/pkcs11>\
Note: YMMV, not all fully matured.

[Exercise \"Symmetry\"]{style="color: rgb(51,153,102);"}

[Optional Exercise \"Trusted Platform
Module\"]{style="color: rgb(51,153,102);"}

[Last Exercise \"Clean up after yourself
please\"]{style="color: rgb(51,153,102);"}

[Optional Demo: a knot instance using SoftHSM, fast rolling and
shortlived RRSIGs]{style="color: rgb(51,153,102);"}

Workshop exercises:
===================

-   **Exercise \"Random in bash\"
    \-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\--**
-   random: bash random commands use (non-blocking) /dev/urandom, not
    real random.\
    Run: RANDOM=13 && echo \$RANDOM  (you got 21880, didn\'t you?)\
    Run twice: echo \$RANDOM (you got 9438 and11869 , didn\'t you?)\
    Now run *all* the commands above again!
-   Linux does have real random: /dev/random (but could be blocking)
-   random: cat /proc/sys/kernel/random/poolsize  → used to be 4096, but
    is now 256 (on kernels \>5, a switch was made)
-   random: cat /proc/sys/kernel/random/entropy\_avail → on kernels \<5
    this drops on intensive use of /dev/random (e.g. od -d /dev/random)
-   /dev/urandom generates a predictable, repeatable set of
    pseudo-random numbers.\
    The begin argument is a starting point, and this can of course be a
    piece of \"good\" random from the pool.
-   For testing a predictable, repeatable set of pseudo-random numbers
    can actually be a good thing!
-   *(After* the PKSC11 exercises you can revisit this exercise and try:
    pkcs11-tool \--module /usr/lib/softhsm/libsofthsm2.so \--token
    Token1 \--generate-random 64 \| xxd -c 64 -p (see random from an
    HSM)
-   **Exercise \"Finland tastes salty\"
    \-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\--**
-   Issue this command (or your OS\'s equivalent): kdig +dnssec +multi
    nsec3param fi. \@1.1.1.1
-   now do .nl
-   now do .se ( Hint: if the penny doesn\'t drop, do
    blah-nonexistant.se. (no nsec3 in .se, so no salt))
-   **Exercise \"**Cyberchef, your tool for all things crypto\"
    \-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\--
-   Visit: [https://cyberchef.io/\#recipe=AES\_Encrypt(%7B\'option\':\'UTF8\',\'string\':\'my\_key1234567890\'%7D,%7B\'option\':\'UTF8\',\'string\':\'0000000000000000\'%7D,\'CBC\',\'Raw\',\'Hex\',%7B\'option\':\'Hex\',\'string\':\'\'%7D)&input=VGhpcyBpcyB0b3Agc2VjcmV0ISEh](https://cyberchef.io/#recipe=AES_Encrypt(%7B)
-   Now change the IV, but not the key and input.
-   Have a look at the left, see what CyberChef can do for you. Now try
    something new like Base64 and unBase64. (Hint: drag & drop)
-   **Exercise \"Introducing SoftHSM2 by NLnet Labs\"
    \-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\--**
-   softhsm: you need to use sudo to use softhsm, if not softhsm only
    visible by user.
-   softhsm: softhsm2-util \--show-slots (always an empty slot available
    for a new token slot)
-   softhsm: man softhsm2-util (what is is, what does it do: list, init,
    delete, import. No signing/crypting!)
-   softhsm: softhsm2-util \--init-token \--free \--label \"Token1\"
    \--pin 0000 \--so-pin 1234 (PIN/\"password\" for user and so-user)
-   softhsm: softhsm2-util \--show-slots (show that a token has been
    inserted, and new free slot was created. \--free means: use first
    empty slot)
-   softhsm: NOTE no keys are in this token yet! (token is just the
    cryptomodule)
-   softhsm:  cat /etc/softhsm/softhsm2.conf (where are the tokens? And
    ls -lR /var/lib/softhsm/tokens/)
-   softhsm: there is an sqlite option, probably recompile needed.
-   softhsm2-util \--delete-token \--token Token1 (get rid of token, for
    deleting objects in the token see below)
-   **Exercise \"Introducing pkcs11-tool from opensc package &
    hash\"\-\-\-\-\-\-\-\-\-\-\-\-\-\--**
-   It\'s far from complete, version 0.23. Good enough for this
    workshop. If you\'re serious you\'d better use the pkcs11 libraries
    of your favorite programming language.
-   man file is not completely helpful, examples on the interwebs are
    often confusing.
-   pkcs11-tool \--module /usr/lib/softhsm/libsofthsm2.so \--show-info 
    (info about the HSM, see how it is linked to softhsm by library)
-   pkcs11-tool \--module /usr/lib/softhsm/libsofthsm2.so \--list-slots 
    (like softhsm2-util \--show-slots, run as root = shows all slots of
    all users)
-   pkcs11-tool \--module /usr/lib/softhsm/libsofthsm2.so
    \--list-mechanisms (show all crypto operations an HSM can do.
    Different HSMs differ)
-   If you have access to a Thales-Safenet-Luna HSM this works:
    pkcs11-tool \--module
    /usr/safenet/lunaclient/lib/libCryptoki2\_64.so
-   pkcs11-tool \--module /usr/lib/softhsm/libsofthsm2.so \--token
    Token1 \--mechanism SHA256 \--hash  \| xxd -p -c 64 (asks for input,
    close with ctrl-d twice)
-   echo -n blah \> blah.txt && pkcs11-tool \--module
    /usr/lib/softhsm/libsofthsm2.so \--token Token1 \--mechanism SHA256
    \--hash -i blah.txt \| xxd -p -c 64\
    With -i and -o, please make sure you have only 1 space between it
    and the filename.
-   echo -n blah \| pkcs11-tool \--module
    /usr/lib/softhsm/libsofthsm2.so \--token Token1 \--mechanism SHA256
    \--hash \|  xxd -p -c 64 (like sha256sum en openssl, your token can
    hash)\
    Thales: pkcs11-tool \--module
    /usr/safenet/lunaclient/lib/libCryptoki2\_64.so \--token hagroup-tst
    \--mechanism SHA256 \--hash \--login -i blah.txt -o blah.hash && cat
    blah.hash \| xxd -p -c 64\
    Hint: get the token name and PIN from ods conf.xml (could use
    \--pin, but unsafe on cli)
-   **Exercise \"pkcs11-tool: gimme some keys & lemme
    in\"\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\--**
-   pkcs11-tool \--module /usr/lib/softhsm/libsofthsm2.so \--token
    Token1 \--keypairgen \--id 1 \--label ec256\_1 \--key-type
    EC:secp256r1 (error, not logged in)
-   pkcs11-tool \--module /usr/lib/softhsm/libsofthsm2.so \--token
    Token1 \--keypairgen \--id 1 \--label ec256\_1 \--key-type
    EC:secp256r1 \--login \--login-type user \--pin 0000
-   pkcs11-tool \--module /usr/lib/softhsm/libsofthsm2.so \--token
    Token1 \--list-objects (only pub part, not logged in)
-   pkcs11-tool \--module /usr/lib/softhsm/libsofthsm2.so \--token
    Token1 \--list-objects \--login \--login-type user \--pin 0000
    (private and public part (same labels!), with login)
-   pkcs11-tool \--module /usr/lib/softhsm/libsofthsm2.so \--token
    Token1 \--list-objects \--pin 0000 (\--pin implies \--login
    \--login-type user)
-   pkcs11-tool \--module /usr/lib/softhsm/libsofthsm2.so \--token
    Token1 \--keypairgen \--id 2 \--label ec256\_2 \--key-type
    EC:prime256v1 \--pin 0000 (second keypair, different curve)
-   pkcs11-tool \--module /usr/lib/softhsm/libsofthsm2.so \--token
    Token1 \--read-object \--type pubkey \--id 2 -o ec256\_2-pub.der
    (output pub part to file, no login needed) (DO NOT use \--label in
    stead of \--id)
-   cat ec256\_2-pub.der \| base64 (there\'s your pub key) (NOTE:
    private part is not exportable)
-   (apt install -y dumpasn1) dumpasn1 ec256\_2-pub.der (representation
    in useful format)
-   Advice: even though you gave your key a label, please always use
    \--id, I\'v see weird fails when using \--label
-   **Exercise \"pkcs11-tool: start signing
    already!\"\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\--**
-   echo -n \'nl.                  3600    IN      SOA     ns1.dns.nl.
    hostmaster.domain-registry.nl. 2023110219 3600 600 2419200 600\' \>
    soa.txt\
    pkcs11-tool \--module /usr/lib/softhsm/libsofthsm2.so \--token
    Token1 \--mechanism SHA256 \--hash -i soa.txt -o soa.hash (why?
    because trying to be real: sigs are made on hashes)\
    pkcs11-tool \--module /usr/lib/softhsm/libsofthsm2.so \--token
    Token1 \--pin 0000 \--sign \--id 2 \--mechanism ECDSA -i soa.hash -o
    soa.sig (Needs PIN, you should know why)\
    cat soa.sig \| base64   (That looks remarkably like an EC RRSIG!)\
    pkcs11-tool \--module /usr/lib/softhsm/libsofthsm2.so \--token
    Token1 \--pin 0000 \--id 2 \--verify -m ECDSA -i soa.hash
    \--signature-file soa.sig (that should work, no PIN needed)
-   if you export the public key, and sign with \--signature-format
    openssl, you can also verify with openssl (EC keeps failing, but RSA
    works fine)\
    pkcs11-tool \--module /usr/lib/softhsm/libsofthsm2.so \--token
    Token1 -k \--key-type rsa:1024 \--id 1005 \--label rsatest5 \--pin
    0000\
    pkcs11-tool \--module /usr/lib/softhsm/libsofthsm2.so \--token
    Token1 \--pin 0000 \--sign \--id 1005 -m SHA512-RSA-PKCS \--input
    soa.hash \--output soa\_rsa.sig\
    pkcs11-tool \--module /usr/lib/softhsm/libsofthsm2.so \--token
    Token1 \--pin 0000 \--verify \--id 1005 -m SHA512-RSA-PKCS
    \--signature-file soa\_rsa.sig \--input soa.hash\
    pkcs11-tool \--module /usr/lib/softhsm/libsofthsm2.so \--token
    Token1 \--pin 0000 \--read-object \--type pubkey \--id 1005 -o
    rsa.der\
    openssl dgst -verify rsa.der -sha512 -signature soa\_rsa.sig
    soa.hash
-   **Exercise \"Symmetry\"
    \-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\--**
-   With pkcs11-tool version 0.22 I never managed to use symmetrical
    encryption (e.g. AES), it is not supported, even though the
    HSM/Token does report it and I can create an AES key with
    pkcs11-tool just fine. \
    pkcs11-tool: unrecognized option \'\--encrypt\'. \
    Version 0.23 does support it. If you have 0.23:
-   echo \'Top secret information\' \> blah.txt\
    pkcs11-tool \--module /usr/lib/softhsm/libsofthsm2.so \--token
    Token1 \--keygen \--key-type AES:16 \--label aes16\_1 \--id 13
    \--pin 0000\
    pkcs11-tool \--module /usr/lib/softhsm/libsofthsm2.so \--token
    Token1 \--pin 0000 \--encrypt \--id 13 -m AES-CBC-PAD \--iv
    \"00000000000000000000000000000000\" -i blah.txt -o
    encrypted\_file.data\
    pkcs11-tool \--module /usr/lib/softhsm/libsofthsm2.so \--token
    Token1 \--pin 0000 \--decrypt \--id 13 -m AES-CBC-PAD \--iv
    \"00000000000000000000000000000000\" -i encrypted\_file.data
-   **Optional Exercise \"Trusted Platform Module\"**
-   Warning: this does not work in a standard VM or LXD container, so
    you would have to install software on your host system, proceed only
    if you are willing to do this.
-   sudo dmesg \| grep -i tpm2 → if you do not have a TPM chip, the next
    commands will not work or be useless.
-   apt install libtpm2-pkcs11-tools libtpm2-pkcs11-1
-   Not strictly needed for the first 3 commands:\
    tpm2\_ptool init \--path=\~/tmp\
    export TPM2\_PKCS11\_STORE=\$HOME/tmp\
    tpm2\_ptool addtoken \--pid=1 \--sopin=1234 \--userpin=0000
    \--label=testing \--path \~/tmp
-   pkcs11-tool \--module
    /usr/lib/x86\_64-linux-gnu/libtpm2\_pkcs11.so.1 \--show-info\
    pkcs11-tool \--module
    /usr/lib/x86\_64-linux-gnu/libtpm2\_pkcs11.so.1 \--list-token-slots\
    pkcs11-tool \--module
    /usr/lib/x86\_64-linux-gnu/libtpm2\_pkcs11.so.1 \--generate-random
    64 \| xxd -c 64 -p  (note: no label needed here)\
    pkcs11-tool \--module
    /usr/lib/x86\_64-linux-gnu/libtpm2\_pkcs11.so.1 \--keypairgen
    \--label=\"testpair\" \--id 01 \--pin 0000\
    pkcs11-tool \--module
    /usr/lib/x86\_64-linux-gnu/libtpm2\_pkcs11.so.1 \--label=\"testing\"
    \--test \--pin 0000
-   **Last Exercise \"Clean up after yourself
    please\"\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\--**
-   pkcs11-tool \--module /usr/lib/softhsm/libsofthsm2.so \--token
    Token1 \--pin 0000 \--delete-object \--type secrkey \--label
    aes16\_1 \--id 13  (if used without label = first found or maybe
    first found without a label)
-   pkcs11-tool \--module /usr/lib/softhsm/libsofthsm2.so \--token
    Token1 \--pin 0000 \--delete-object \--label ec256\_2 \--id 2
    \--type pubkey\
    pkcs11-tool \--module /usr/lib/softhsm/libsofthsm2.so \--token
    Token1 \--pin 0000 \--delete-object \--label ec256\_2 \--id 2
    \--type privkey
-   softhsm2-util \--delete-token \--token \'Token1\'
-   should you have initialized your TPM: tpm2\_ptool destroy \--pid
    \<your id\> → error → rm the dir mentioned in the error (WARNING
    make sure you should actually be doing this)
-   \-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\--

Optional Demo: a knot instance using SoftHSM, fast rolling and shortlived RRSIGs.
```
lxc delete knothsm01
lxc launch images:debian/12 knothsm01

lxc shell knothsm01
apt install -y less man softhsm2 opensc knot knot-dnssecutils knot-dnsutils

echo "set mouse-=a" > ~/.vimrc    # Make vim behave normally when copy-pasting. Debian-only thing?

usermod -G softhsm knot
su - knot -s /bin/bash -c 'softhsm2-util --init-token --free --label knot --pin 0000 --so-pin 1234'
su - knot -s /bin/bash -c 'pkcs11-tool --module /usr/lib/softhsm/libsofthsm2.so --list-token-slots'

vim /etc/knot/knot.conf
------------------------------------------
server:
    rundir: "/run/knot"
    user: knot:knot
    automatic-acl: on
    listen: [ 127.0.0.1@53, ::1@53 ]

log:
  - target: syslog
    any: info

database:
    storage: "/var/lib/knot"

keystore:
   - id: SoftHSM
     backend: pkcs11
     config: "pkcs11:token=knot;pin-value=0000 /usr/lib/softhsm/libsofthsm2.so"
     key-label: true

submission:
  - id: unsafe
    timeout: 10s

policy:
  - id: automatic-fast
    manual: off
    keystore: SoftHSM
    algorithm: ecdsap256sha256
    ksk-lifetime: 60m
    zsk-lifetime: 30m
    propagation-delay: 2s
    delete-delay: 10m
    dnskey-ttl: 300s
    zone-max-ttl: 300s
    rrsig-lifetime: 15m
    rrsig-refresh: 7m
    rrsig-pre-refresh: 3m
    ksk-submission: unsafe
    cds-cdnskey-publish: none
  - id: manual
    manual: on
    keystore: SoftHSM

remote:

template:
  - id: default
    storage: "/var/lib/knot"
    file: "%s.zone"
    dnssec-signing: on
    dnssec-policy: automatic-fast

zone:
  - domain: example.com
    dnssec-policy: manual
  - domain: example.net
------------------------------------------
knotc conf-check

vim /var/lib/knot/example.com.zone /var/lib/knot/example.net.zone
----------------
$ORIGIN example.com.
$TTL 5m
@       SOA     ns1 hostmaster 1000 5m 1m 10m 4m
        NS      ns1
ns1     A       127.0.0.1
----------------
chown knot:knot /var/lib/knot/example.*.zone
kzonecheck -v --dnssec off /var/lib/knot/example.com.zone
kzonecheck -v --dnssec off /var/lib/knot/example.net.zone
 
systemctl restart knot
systemctl status knot
journalctl -fexu knot.service
 
su - knot -s /bin/bash -c '/usr/sbin/keymgr example.com. generate algorithm=13 ksk=yes zsk=no'
su - knot -s /bin/bash -c '/usr/sbin/keymgr example.com. generate algorithm=13 ksk=no zsk=yes'
su - knot -s /bin/bash -c 'pkcs11-tool --module /usr/lib/softhsm/libsofthsm2.so --list-objects --pin 0000'

su - knot -s /bin/bash -c '/usr/sbin/keymgr example.com list'
su - knot -s /bin/bash -c '/usr/sbin/keymgr example.com list -e'
 
knotc zone-reload example.com
systemctl status knot
kzonecheck -v --dnssec on /var/lib/knot/example.com.zone
cat /var/lib/knot/example.com.zone

knotc zone-status
knotc zone-read example.com @ SOA
kdig @::1 +norec +dnssec +cd +multi soa example.com
kdig @::1 +norec +dnssec +cd +multi dnskey example.com

knotc zone-backup -b +backupdir /var/tmp/backup_example.com +journal example.com.
vim /var/lib/knot/example.com.zone (make some edit, e.g. add www.example.com)
knotc zone-reload example.com

kdig @::1 +norec +dnssec +cd +multi soa example.com
kdig @::1 +norec +dnssec +cd +multi www.example.com
kdig @::1 +norec +dnssec +cd +multi nonexistant.example.com

------------------------------------
From Knot docu:
Import key pair in HSM
openssl rsa -outform DER -in c4eae5dea3ee8c15395680085c515f2ad41941b6.pem \
  -out c4eae5dea3ee8c15395680085c515f2ad41941b6.priv.der

openssl rsa -outform DER -in c4eae5dea3ee8c15395680085c515f2ad41941b6.pem \
  -out c4eae5dea3ee8c15395680085c515f2ad41941b6.pub.der -pubout

pkcs11-tool --module /usr/local/lib/pkcs11.so --login \
  --write-object c4eae5dea3ee8c15395680085c515f2ad41941b6.priv.der --type privkey \
  --usage-sign --id c4eae5dea3ee8c15395680085c515f2ad41941b6

pkcs11-tool --module /usr/local/lib/pkcs11.so -login \
  --write-object c4eae5dea3ee8c15395680085c515f2ad41941b6.pub.der --type pubkey \
  --usage-sign --id c4eae5dea3ee8c15395680085c515f2ad41941b6
```

OpenDNSsec conf.xml example:
```
<Configuration>
        <RepositoryList>
                <Repository name="SoftHSM">
                        <Module>/usr/lib/softhsm/libsofthsm2.so</Module>
                        <TokenLabel>OpenDNSSEC</TokenLabel>
                        <PIN>1234</PIN>
                </Repository>
        </RepositoryList>
```
