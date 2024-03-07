---------
Optional Demo: a knot instance using SoftHSM, fast rolling and shortlived RRSIGs


Interesting commands
```
add-apt-repository ppa:cz.nic-labs/knot-dns-latest
apt install -y knot knot-dnssecutils knot-dnsutils less softhsm2 opensc
usermod -aG softhsm knot
su - knot -s /bin/bash -c 'softhsm2-util --init-token --free --label knot --pin 0000 --so-pin 1234'
ls -lR /var/lib/softhsm/tokens/
pkcs11-tool --module /usr/lib/softhsm/libsofthsm2.so --list-slots
kzonecheck -v --dnssec off /var/lib/knot/zones/example.com.zone
su - knot -s /bin/bash -c '/usr/sbin/keymgr example.com list'
su - knot -s /bin/bash -c '/usr/sbin/keymgr example.com list -e'
knotc zone-status
journalctl -fexu knot
```


----------
/var/lib/knot/zones/example.com.zone
```
$ORIGIN example.com.
$TTL 5m
@       SOA     ns1 hostmaster 1000 5m 1m 10m 4m
        NS      ns1
ns1     A       127.0.0.1
```

-----------
/etc/knot/knot.conf 
```
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
     # usermod -aG softhsm knot
     backend: pkcs11
     config: "pkcs11:token=knot;pin-value=0000 /usr/lib/softhsm/libsofthsm2.so"
     key-label: true

submission:
  - id: unsafe
    timeout: 10s
  - id: safe
    timeout: 3d 

policy:
  - id: automatic-fast-softhsm
    manual: off
    keystore: SoftHSM
    algorithm: ecdsap256sha256
    ksk-lifetime: 0
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
  - id: automatic-softhsm
    manual: off
    keystore: SoftHSM
    algorithm: ecdsap256sha256
    dnskey-ttl: 30m
    ksk-lifetime: 0
    zsk-lifetime: 90d
    propagation-delay: 2h
    delete-delay: 14d
    zone-max-ttl: 30m
    rrsig-lifetime: 14d
    rrsig-refresh: 8d
    rrsig-pre-refresh: 5h
    reproducible-signing: on
    ksk-submission: safe
    cds-cdnskey-publish: none
    nsec3: on
    nsec3-iterations: 0
    nsec3-opt-out: off
    nsec3-salt-length: 0
    nsec3-salt-lifetime: 90d

remote:

template:
  - id: default
    storage: "/var/lib/knot/zones"
    file: "%s.zone"

zone:
  - domain: example.com
    dnssec-signing: on
    dnssec-policy: automatic-fast-softhsm
```
