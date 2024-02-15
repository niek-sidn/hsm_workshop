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
## Exercise \"Symmetry\"
-   With pkcs11-tool version 0.22 I never managed to use symmetrical encryption (e.g. AES), it is not supported, even though the
    HSM/Token does report it and I can create an AES key with pkcs11-tool just fine.
    pkcs11-tool: unrecognized option \'\--encrypt\'.
    Version 0.23 does support it. If you have 0.23:
-   echo \'Top secret information\' \> blah.txt
    pkcs11-tool \--module /usr/lib/softhsm/libsofthsm2.so \--token Token1 \--keygen \--key-type AES:16 \--label aes16\_1 \--id 13 \--pin 0000
    pkcs11-tool \--module /usr/lib/softhsm/libsofthsm2.so \--token
    Token1 \--pin 0000 \--encrypt \--id 13 -m AES-CBC-PAD \--iv
    \"00000000000000000000000000000000\" -i blah.txt -o
    encrypted\_file.data
    pkcs11-tool \--module /usr/lib/softhsm/libsofthsm2.so \--token
    Token1 \--pin 0000 \--decrypt \--id 13 -m AES-CBC-PAD \--iv
    \"00000000000000000000000000000000\" -i encrypted\_file.data


---------------
## Optional Exercise \"Trusted Platform Module\"**
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

----------
## Last Exercise \"Clean up after yourself please\"
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
    \<your id\> → error → rm the dir mentioned in the error (***WARNING make sure you should actually be doing this***)

--------------------
[Next](https://github.com/niek-sidn/hsm_workshop/blob/main/Slide18.md)
