----------------------
APT based systems
```
Prep
-------------------
apt update && apt upgrade
apt install pcscd libccid libpcsclite-dev libssl-dev libreadline-dev autoconf automake build-essential docbook-xsl xsltproc libtool pkg-config

OpenSC
-------------------
git clone https://github.com/OpenSC/OpenSC.git
cd OpenSC/
./bootstrap
./configure --prefix=/usr --sysconfdir=/etc/opensc
make
make install
```
[Also see the project on GitHub](https://github.com/OpenSC/OpenSC/wiki/Compiling-and-Installing-on-Unix-flavors)


----------------------
