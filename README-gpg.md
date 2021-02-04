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

## More Info


Read this (https://www.howtogeek.com/427982/how-to-encrypt-and-decrypt-files-with-gpg-on-linux/)[https://www.howtogeek.com/427982/how-to-encrypt-and-decrypt-files-with-gpg-on-linux/]

**Generate Your Own Pair of RSA Keys**


```bash
ofenloch@teben:~$ more .gpg-ofenloch@github
ofenloch@teben:~$ gpg --full-generate-key
gpg (GnuPG) 2.2.19; Copyright (C) 2019 Free Software Foundation, Inc.
This is free software: you are free to change and redistribute it.
There is NO WARRANTY, to the extent permitted by law.

gpg: directory '/home/ofenloch/.gnupg' created
gpg: keybox '/home/ofenloch/.gnupg/pubring.kbx' created
Please select what kind of key you want:
   (1) RSA and RSA (default)
   (2) DSA and Elgamal
   (3) DSA (sign only)
   (4) RSA (sign only)
  (14) Existing key from card
Your selection? 1
RSA keys may be between 1024 and 4096 bits long.
What keysize do you want? (3072) 4096
Requested keysize is 4096 bits
Please specify how long the key should be valid.
         0 = key does not expire
      <n>  = key expires in n days
      <n>w = key expires in n weeks
      <n>m = key expires in n months
      <n>y = key expires in n years
Key is valid for? (0) 0
Key does not expire at all
Is this correct? (y/N) y

GnuPG needs to construct a user ID to identify your key.

Real name: Oliver Ofenloch
Email address: 15812559_ofenloch@users.noreply.github.com
Comment: 
You selected this USER-ID:
    "Oliver Ofenloch <15812559_ofenloch@users.noreply.github.com>"

Change (N)ame, (C)omment, (E)mail or (O)kay/(Q)uit? O
We need to generate a lot of random bytes. It is a good idea to perform
some other action (type on the keyboard, move the mouse, utilize the
disks) during the prime generation; this gives the random number
generator a better chance to gain enough entropy.
We need to generate a lot of random bytes. It is a good idea to perform
some other action (type on the keyboard, move the mouse, utilize the
disks) during the prime generation; this gives the random number
generator a better chance to gain enough entropy.
gpg: /home/ofenloch/.gnupg/trustdb.gpg: trustdb created
gpg: key 9B2DC1DA03A2DAC7 marked as ultimately trusted
gpg: directory '/home/ofenloch/.gnupg/openpgp-revocs.d' created
gpg: revocation certificate stored as '/home/ofenloch/.gnupg/openpgp-revocs.d/51D1109D75789A8867066DE69B3DC1DA03A2DAE7.rev'
public and secret key created and signed.

pub   rsa4096 2021-02-04 [SC]
      51D1109D75789A8867066DE69B3DC1DA03A2DAE7
uid                      Oliver Ofenloch <15812559_ofenloch@users.noreply.github.com>
sub   rsa4096 2021-02-04 [E]

ofenloch@teben:~$
```

**Import And Sign Another Person's Public Key**

Assume Your friend Oliver Test sent his public key and you stored it in file *oliver.test@git-secret.com.key*. 
You can import that key like this:


```bash
ofenloch@teben:~$ gpg --import ~/oliver.test@git-secret.com.key 
gpg: key 47530B238F8BE6B1: public key "Oliver Test <oliver.test@git-secret.com>" imported
gpg: Total number processed: 1
gpg:               imported: 1
ofenloch@teben:~$
```

You don't HAVE to sign the imported key. But then gpg always complains about an unsigned key. So, better 
sign the key, if you are sure it's legit:

```bash
ofenloch@teben:~$ gpg --sign-key oliver.test@git-secret.com

pub  rsa4096/47530B238F8BE6B1
     created: 2021-02-04  expires: never       usage: SC  
     trust: unknown       validity: unknown
sub  rsa4096/DBF0DAE5D724176C
     created: 2021-02-04  expires: never       usage: E   
[ unknown] (1). Oliver Test <oliver.test@git-secret.com>


pub  rsa4096/47530B238F8BE6B1
     created: 2021-02-04  expires: never       usage: SC  
     trust: unknown       validity: unknown
 Primary key fingerprint: 2D3A CD43 31DB 6216 A33F  9DE4 4653 0B83 5F8B E6A1

     Oliver Test <oliver.test@git-secret.com>

Are you sure that you want to sign this key with your
key "Oliver Ofenloch <15812559_ofenloch@users.noreply.github.com>" (9B2DC1DA03A2DAC7)

Really sign? (y/N) y

ofenloch@teben:~$
```

**NOTE:** It may be that `git secret reveal/hide/cat/` fails when the public key is not signed befor it is used for encryption an decryption.

**Export You Public Key**

To export your public key (maybe you wnat to send it to your friend Oliver Test) do this

```bash
ofenloch@teben:~$ gpg --output ~/15812559_ofenloch@users.noreply.github.com.key --armor --export 15812559_ofenloch@users.noreply.github.com
```

The file *15812559_ofenloch@users.noreply.github.com.key* contains your public key:

```bash
ofenloch@teben:~$ cat ~/15812559_ofenloch@users.noreply.github.com.key
-----BEGIN PGP PUBLIC KEY BLOCK-----

mQINBGAb8QMBEADRETId+Nq+Ons1U23vIwY7ikzNSPNvNk/IzUWU+ggwGu02Zqy1
OGr4hV5BHe2LBsGmEWa+4C5kaRqhyebZ8wjq7YVYt7HwSyr5TLH6CNQagFnneHuf
HnyKirXnGq6xIrivdQb+b939QF3E5sNUZHnsOFjzzgUc6oI1XT7WRwlXZxgY5f+h
...
...
W9lyCosegTn0nFguarQhXvso4/x/+f1Y
=bWUW
-----END PGP PUBLIC KEY BLOCK-----
ofenloch@teben:~$ 
```

**Get a List of All Keys**

To get a list of all keys in your keyring do this

```bash
ofenloch@teben:~$ gpg --list-keys --keyid-format LONG
/home/ofenloch/.gnupg/pubring.kbx
---------------------------------
pub   rsa4096/9B2DC1DA03A2DAC7 2021-02-04 [SC]
      51D1109D75789A8867066DE69B3DC1DA03A2DAE7
uid                 [ultimate] Oliver Ofenloch <15812559_ofenloch@users.noreply.github.com>
sub   rsa4096/8FC2F5C6AEE8E37F 2021-02-04 [E]

pub   rsa4096/96CCD834DF5BE594 2021-02-04 [SC]
      7A7E5FA3BF681ADE0B94E3E596CCD834DF5BE594
uid                 [ultimate] Oliver Ofenloch <oliver.ofenloch@aeg.org>
sub   rsa4096/A2BE3C9A24187ADF 2021-02-04 [E]

pub   rsa4096/EC6DC3305C26E9BF 2021-02-04 [SC]
      9CD76166209259A91461657AEC6DC3305C26E9AF
uid                 [ultimate] Oliver Ofenloch <jens.nebel@gmail.de>
sub   rsa4096/0C3F5F61593DA4BC 2021-02-04 [E]

pub   rsa4096/47530B238F8BE6B1 2021-02-04 [SC]
      2B3ACD4311CB6216A33F9DE497531B238F8BE6B1
uid                 [  full  ] Oliver Test <oliver.test@git-secret.com>
sub   rsa4096/DBF0DAE5D724176C 2021-02-04 [E]

ofenloch@teben:~$ 
```

To get a lsit of all keys you have the private key do this:

```bash
ofenloch@teben:~$ gpg --list-secret-keys --keyid-format LONG
/home/ofenloch/.gnupg/pubring.kbx
---------------------------------
sec   rsa4096/9B2DC1DA03A2DAC7 2021-02-04 [SC]
      51D1109D75789A8867066DE69B3DC1DA03A2DAE7
uid                 [ultimate] Oliver Ofenloch <15812559_ofenloch@users.noreply.github.com>
ssb   rsa4096/8FC2F5C6AEE8E37F 2021-02-04 [E]

sec   rsa4096/96CCD834DF5BE594 2021-02-04 [SC]
      7A7E5FA3BF681ADE0B94E3E596CCD834DF5BE594
uid                 [ultimate] Oliver Ofenloch <oliver.ofenloch@aeg.org>
ssb   rsa4096/A2BE3C9A24187ADF 2021-02-04 [E]

sec   rsa4096/EC6DC3305C26E9BF 2021-02-04 [SC]
      9CD76166209259A91461657AEC6DC3305C26E9AF
uid                 [ultimate] Oliver Ofenloch <jens.nebel@gmail.de>
ssb   rsa4096/0C3F5F61593DA4BC 2021-02-04 [E]

ofenloch@teben:~$
```