------------------------------
# Invitation and getting ready

------------------------------
## Aim of this Workshop
Mid 2023 people started showing interest in some sort of HSM "Course".
Since then even more people got interested every time we mentioned it.
Specifically it would have to be some hands-on thing, because IT-people cannot live on theory alone.
So "Course" became "Workshop".
People asked for a focus on general HSM knowledge, not on the Thales/Safenet/Luna devices we employ.
Also "HSM and Cloud" should be a thing in this workshop people told us.
The workshop is in English (well... Dunglish) because our Friends from
InternetStiftelsen also want to do this workshop.
So the purpose of the workshop became:
- getting to know what an HSM is, and what you can do with it (for what type of problem could it be a solution?).
- throwing more than a few commands at an HSM to see what happens.
- after this workshop you should have gained some confidence when
working with an HSM or when researching HSM stuff on your own.

-----------------
## Setup of the workshop
Theory mixed with commandline work to make it less boring and get
experienced (sorry, Jimi Hendrix was playing while writing this)

For the hands-on work you can use any host you can install software
packages on and that has a shell.
I'm using Ubuntu 22.04 Linux and bash on a LXD container on my laptop. You
choose your own, but as always ymmv.

If you got confused already, please ask one of the experienced people to
help you. I can provide a linux shell if you want, please ask me. You'll still
need Putty or equivalent ssh-client to use it.

So you Need:

-   a Linux* commandline to issue hsm and pkcs11 commands.
-   an HSM you can use or install SoftHSM on your host.
-   I repeat: I can provide you with this, if you ask me beforehand.

*)You can try doing this workshop on Mac or Windows if you want, but
I'm not really able to help you beyond this:\
Windows Installer for SoftHSM:[SoftHSM2-for-windows](https://github.com/disig/SoftHSM2-for-windows)\
Windows Installer for OpenSC: [OpenSC](https://github.com/OpenSC/OpenSC)\
Mac: ???

You can also use a Docker container to do the workshop. It contains all the tools needed during the workshop:
```bash
docker run -ti marknl/hsmworkshop /bin/bash
```

-------------------
## Prior knowledge
There is no time to explain things beyond HSM\'s, so you should:

-   know what the essence of assymmetric (public key) and symmetric
    encryption is.
-   know that you can also do digital signing with a public-private
    key-pair.
    (that is basically what DNSsec signing is all about).
-   use youtube if you lack this knowledge.

-------------------------
[Next](https://github.com/niek-sidn/hsm_workshop/blob/main/Slide01.md)
