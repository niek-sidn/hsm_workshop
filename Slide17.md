## Other PKCS#11 interfaces
Java Cryptography Extensions, MS CryptoNG.\
And several libraries that use PKSC11 in the background, so you don't
have to deal with it very much.\
<https://python-pkcs11.readthedocs.io/en/latest/>\
<https://github.com/bentonstark/py-hsm>\
<https://github.com/LudovicRousseau/PyKCS11>  &&
 <https://aws.amazon.com/blogs/apn/signing-data-using-keys-stored-in-aws-cloudhsm-with-python/>\
<https://github.com/miekg/pkcs11>\
Note: YMMV, not all fully matured.

--------------------
## Exercise "Symmetry"
Note: With pkcs11-tool version 0.22 I never managed to use symmetrical encryption (e.g. AES), it is not supported, even though the
HSM/Token does report it and I can create an AES key with pkcs11-tool just fine.
Error: pkcs11-tool: unrecognized option '--encrypt'.

-------------------
Version 0.23 does support it. If you have 0.23:
```
echo 'Top secret information' > blah.txt
pkcs11-tool --module /usr/lib/softhsm/libsofthsm2.so --token Token1 --keygen --key-type AES:16 --label aes16_1 --id 13 --pin 0000
pkcs11-tool --module /usr/lib/softhsm/libsofthsm2.so --token Token1 --pin 0000 --encrypt --id 13 -m AES-CBC-PAD --iv "00000000000000000000000000000000" -i blah.txt -o encrypted_file.data
pkcs11-tool --module /usr/lib/softhsm/libsofthsm2.so --token Token1 --pin 0000 --decrypt --id 13 -m AES-CBC-PAD --iv "00000000000000000000000000000000" -i encrypted_file.data
```

---------------
## Optional Exercise "Trusted Platform Module"
Warning: this does not work in a standard VM or Docker/LXD container, so
you would have to install software on your host system, proceed only
if you are willing to do this.

```
sudo dmesg | grep -i tpm2
```
if you do not have a TPM chip, the next commands will not work or be useless.


Install the pkcs11 packages for tpm
```
sudo apt install libtpm2-pkcs11-tools libtpm2-pkcs11-1
```

--------------
Try the following commands:
 (note: no --token needed here)\
```
sudo pkcs11-tool --module /usr/lib/x86_64-linux-gnu/libtpm2_pkcs11.so.1 --show-info
sudo pkcs11-tool --module /usr/lib/x86_64-linux-gnu/libtpm2_pkcs11.so.1 --list-token-slots
sudo pkcs11-tool --module /usr/lib/x86_64-linux-gnu/libtpm2_pkcs11.so.1 --generate-random 64 | xxd -c 64 -p
```

--------------
If you want you can create a token and a key pair
```
tpm2_ptool init --path=~/tmp
export TPM2_PKCS11_STORE=$HOME/tmp
tpm2_ptool addtoken --pid=1 --sopin=1234 --userpin=0000 --label=testing --path ~/tmp
pkcs11-tool --module /usr/lib/x86_64-linux-gnu/libtpm2_pkcs11.so.1 --token testing --test --pin 0000
p11tool --provider /usr/lib/x86_64-linux-gnu/libtpm2_pkcs11.so.1 --list-all
pkcs11-tool --module /usr/lib/x86_64-linux-gnu/libtpm2_pkcs11.so.1 --keypairgen --token testing --label=testpair --id 01 --pin 0000
pkcs11-tool --module /usr/lib/x86_64-linux-gnu/libtpm2_pkcs11.so.1 --token testing --list-objects --pin 0000
```
Left as an exercise: do some signing and/or encryption like we saw in this workshop.

----------
## Last Exercise "Clean up after yourself please"
```
pkcs11-tool --module /usr/lib/softhsm/libsofthsm2.so --token Token1 --pin 0000 --delete-object --type secrkey --label aes16_1 --id 13
pkcs11-tool --module /usr/lib/softhsm/libsofthsm2.so --token Token1 --pin 0000 --delete-object --label ec256_2 --id 2 --type pubkey
pkcs11-tool --module /usr/lib/softhsm/libsofthsm2.so --token Token1 --pin 0000 --delete-object --label ec256_2 --id 2 --type privkey
softhsm2-util --delete-token --token 'Token1'
```

should you have initialized your TPM:
(***WARNING Please make sure you are not removing anything that you still need***)
```
tpm2_ptool destroy --pid <your id>
```
Always Error, so rm the dir mentioned in the error
E.g. rm /root/tmp/tpm2_pkcs11.sqlite3

--------------------
[Next](https://github.com/niek-sidn/hsm_workshop/blob/main/Slide18.md)
