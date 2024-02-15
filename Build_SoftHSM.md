------------------------
RPM based systems
```
Prep
-------------
yum group install 'Development Tools' -y
yum install libtool-ltdl-devel openssl-devel sqlite-devel -y
yum install python-ply -y
yum install mlocate man -y && updatedb

SoftHSM
---------------
git clone https://github.com/opendnssec/SoftHSMv2.git
cd SoftHSMv2/
sh autogen.sh
./configure --with-objectstore-backend-db
make
make install
cd
updatedb
locate libsofthsm2.so
softhsm2-util --show-slots
sed -i 's/objectstore.backend = file/objectstore.backend = db/' /etc/softhsm2.conf
```

----------------------
APT based systems
```
Prep
-------------------
apt update && apt upgrade
apt install build-essential libssl-dev libsqlite3-dev autogen autoconf libtool plocate

SoftHSM
-------------------
git clone https://github.com/opendnssec/SoftHSMv2.git
cd SoftHSMv2/
sh autogen.sh
./configure --with-objectstore-backend-db
make
make install
cd
updatedb
locate libsofthsm2.so
softhsm2-util --show-slots
sed -i 's/objectstore.backend = file/objectstore.backend = db/' /etc/softhsm2.conf
```

----------------------
