---------------
## Salt (continued)

The KSK-ZSK "key-subkey" mechanism is nice, you do not have to contact
IANA every time you change a key.\
But knowing the above, a ZSK also "protects" the KSK against
over-use (for too long, too often(?)).\
You use the KSK only when signing a new DNSKEY RRset, probably < 10
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

---------------------
## Exercise "Cyberchef, your tool for all things crypto"
Visit: [CyberChef](https://cyberchef.io/#recipe=AES_Decrypt(%7B'option':'UTF8','string':'my_key1234567890'%7D,%7B'option':'UTF8','string':'0000000000000000'%7D,'CBC','Hex','Raw',%7B'option':'Hex','string':''%7D,%7B'option':'Hex','string':''%7D)&input=NDBiNmJhMWM1ZDI0ZDkzZjEwYmFhZTkzYzRmN2E5NzNhOGQ5YzQ4MDBiYmUyZmM0MzRlMTZiMTVjNzNjYTUxZg)

Now change the IV, but not the key and input. Here IV and salt differ.

Have a look at the left, see what CyberChef can do for you.

Now try something new like Base64 and unBase64. (Hint: drag & drop)

---------------
[Next](https://github.com/niek-sidn/hsm_workshop/blob/main/Slide09.md)
