# GPG - Quick and Dirty

You can follow a quick (gpg tutorial at devdungeon)[https://www.devdungeon.com/content/gpg-tutorial]. 

Here are the most useful commands to get started:

To generate a RSA key-pair, run

    gpg --gen-key

To export your public key, run

    gpg --export your.email@address.com > public-key.gpg

To import the public key of someone else (to share the secret with them for instance), run

    gpg --import public-key.gpg

To make sure you get the original public keys of the indicated persons, be sure to use a 
secure channel to transfer it, or use a service you trust, preferably one that uses 
encryption such as Keybase, to retrieve their public key. Otherwise you could grant 
the wrong person access to your secrets by mistake!
