This is work in progress

[https://github.com/latchset/kryoptic/](https://github.com/latchset/kryoptic/)  
For the first steps you can follow the Readme.  
This does not yet result is a working software-HSM!  

After building do a `cargo install --path .` for a smaller (stripped?) .so in the "released" directory.

Now follow the first steps on [https://rcritten.wordpress.com/2024/10/01/trying-a-new-pkcs11-driver-kryoptic/](https://rcritten.wordpress.com/2024/10/01/trying-a-new-pkcs11-driver-kryoptic/)  
Most importantly: `export KRYOPTIC_CONF=/path/to/not/yet/existing/Token1.sql`  
Without this you have no slots in your software-HSM.  
Looks like we get 1 token per .sql

    root@kryoptic:~# pkcs11-tool --module /usr/lib/x86_64-linux-gnu/pkcs11/libkryoptic_pkcs11.so --list-token-slots
    Available slots:
    No slots.
    
    root@kryoptic:~# export KRYOPTIC_CONF=/root/kryoptic/token.sql
    root@kryoptic:~# pkcs11-tool --module /usr/lib/x86_64-linux-gnu/pkcs11/libkryoptic_pkcs11.so --init-token --label Token1
    root@kryoptic:~# pkcs11-tool --module /usr/lib/x86_64-linux-gnu/pkcs11/libkryoptic_pkcs11.so --token Token1 --init-pin --login --pin

    root@kryoptic:~# echo -n foobar | pkcs11-tool --module /usr/lib/x86_64-linux-gnu/pkcs11/libkryoptic_pkcs11.so --hash --mechanism SHA256 | xxd -p -c 64
    Using slot 0 with a present token (0x0)
    Using digest algorithm SHA256
    c3ab8ff13720e8ad9047dd39466b3c8974e592c2fa383d4a3960714caef0c4f2
    
    root@kryoptic:~# echo -n foobar | pkcs11-tool --module /usr/lib/softhsm/libsofthsm2.so --hash --mechanism SHA256 | xxd -p -c 64
    Using slot 0 with a present token (0x205694f9)
    Using digest algorithm SHA256
    c3ab8ff13720e8ad9047dd39466b3c8974e592c2fa383d4a3960714caef0c4f2
    
    root@kryoptic:~# pkcs11-tool --module /usr/lib/x86_64-linux-gnu/pkcs11/libkryoptic_pkcs11.so --generate-random 64 | xxd -c 64 -p
    Using slot 0 with a present token (0x0)
    8c2b473f9be000c90ce646cdf3b1904cec0465d321872d63d517a166501e329e63fd1dfadb371b573c221dce20ac53c7f608de6dd0b2104260177653f5f81edc

    root@kryoptic:~# pkcs11-tool --module /usr/lib/x86_64-linux-gnu/pkcs11/libkryoptic_pkcs11.so --token Token1 --keygen --key-type AES:16 --label aes16_1 --id 13 --pin 0000
    root@kryoptic:~# pkcs11-tool --module /usr/lib/x86_64-linux-gnu/pkcs11/libkryoptic_pkcs11.so --token Token1 --pin 0000 --encrypt --id 13 -m AES-CBC-PAD --iv "00000000000000000000000000000000" -i blah.txt -o encrypted_file.data
    root@kryoptic:~# pkcs11-tool --module /usr/lib/x86_64-linux-gnu/pkcs11/libkryoptic_pkcs11.so --token Token1 --pin 0000 --decrypt --id 13 -m AES-CBC-PAD --iv "00000000000000000000000000000000" -i encrypted_file.data
    
