------------------------
```
Prep for RPM based systems
----------------------
yum group install 'Development Tools' -y
yum install libtool-ltdl-devel openssl-devel sqlite-devel -y
yum install python-ply -y
yum install mlocate man -y && updatedb
----------------------

Prep for APT based systems
-------------------
apt update && apt upgrade
apt install -y --no-install-recommends build-essential libssl-dev libsqlite3-dev autogen autoconf libtool plocate automake
-------------------


SoftHSM
-----------------------------------
git clone https://github.com/opendnssec/SoftHSMv2.git
cd SoftHSMv2/
sh autogen.sh
./configure --with-objectstore-backend-db --prefix=/usr --sysconfdir=/etc/softhsm --localstatedir=/var
make
make install
cd
updatedb
locate libsofthsm2.so
softhsm2-util --show-slots
sed -i 's/objectstore.backend = file/objectstore.backend = db/' /etc/softhsm/softhsm2.conf
-----------------------------------
```

----------------------
