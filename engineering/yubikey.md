### OpenPGP

OpenPGP keys normally have three parts: a single master key, one or more subkeys, and one or more user ids.

The master key is the most important key. Having the private half of the master key proves that you own the OpenPGP key. The master key is used to add/remove subkeys as well as to sign/certify other people’s keys. You don’t need to have the master key present for everyday signing and encryption. If possible, the master key should be kept offline and only used when adding or revoking subkeys or when certifying another person’s PGP key.

Subkeys make maintenance of a OpenPGP key easier. Subkeys can be used for signing data, encrypting data, and/or for authentication. The lifetime and purpose (encrypt,sign,authenticate) of a subkey is controlled by the master key. Subkeys can be added and removed from the PGP key at any time by the owner of the master key.

Subkeys can be installed on a computer that does not have access to the master key. On that computer, the subkeys will be used for encryption/decryption and signing. If the subkeys (or computer) are ever stolen, the master key can then be used to revoke the stolen subkeys and to add new subkeys to the PGP key. This can all be done without generating a new PGP key as long as the master key was not also stolen.

### Generating the master key

```bash
$ gpg --version

gpg (GnuPG) 2.2.4     ## Check so you using gpg version 2
libgcrypt 1.8.1
Copyright (C) 2017 Free Software Foundation, Inc.
License GPLv3+: GNU GPL version 3 or later <https://gnu.org/licenses/gpl.html>
This is free software: you are free to change and redistribute it.
There is NO WARRANTY, to the extent permitted by law.

Home: /home/daniel/.gnupg
Supported algorithms:
Pubkey: RSA, ELG, DSA, ECDH, ECDSA, EDDSA
Cipher: IDEA, 3DES, CAST5, BLOWFISH, AES, AES192, AES256, TWOFISH,
        CAMELLIA128, CAMELLIA192, CAMELLIA256
Hash: SHA1, RIPEMD160, SHA256, SHA384, SHA512, SHA224
Compression: Uncompressed, ZIP, ZLIB, BZIP2
```

```bash
$ gpg --full-generate-key

gpg --full-generate-key
gpg (GnuPG) 2.2.4; Copyright (C) 2017 Free Software Foundation, Inc.
This is free software: you are free to change and redistribute it.
There is NO WARRANTY, to the extent permitted by law.

Please select what kind of key you want:
   (1) RSA and RSA (default)
   (2) DSA and Elgamal
   (3) DSA (sign only)
   (4) RSA (sign only)
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
Key is valid for? (0) 10y
Key expires at Fri 08 Sep 2028 10:47:04 AM CEST
Is this correct? (y/N) y

GnuPG needs to construct a user ID to identify your key.

Real name: Daniel Törbacka
Email address: daniel.torbacka@gmail.com
Comment: Taco
You are using the 'utf-8' character set.
You selected this USER-ID:
    "Daniel Törbacka (Taco) <daniel.torbacka@gmail.com>"

Change (N)ame, (C)omment, (E)mail or (O)kay/(Q)uit? O
We need to generate a lot of random bytes. It is a good idea to perform
some other action (type on the keyboard, move the mouse, utilize the
disks) during the prime generation; this gives the random number
generator a better chance to gain enough entropy.
We need to generate a lot of random bytes. It is a good idea to perform
some other action (type on the keyboard, move the mouse, utilize the
disks) during the prime generation; this gives the random number
generator a better chance to gain enough entropy.
gpg: key 6534B7A6ED35A925 marked as ultimately trusted
gpg: directory '/home/daniel/.gnupg/openpgp-revocs.d' created
j flkgjsagpg: revocation certificate stored as '/home/daniel/.gnupg/openpgp-revocs.d/3C75A125BE5B9622A489CDAB6534B7A6ED35A925.rev'
public and secret key created and signed.

pub   rsa4096 2018-09-11 [SC] [expires: 2028-09-08]
      3C75A125BE5B9622A489CDAB6534B7A6ED35A925
uid                      Daniel Törbacka (Taco) <daniel.torbacka@gmail.com>
sub   rsa4096 2018-09-11 [E] [expires: 2028-09-08]

```

```bash
# Create variable to be used during the gude. 
$ KEY_ID=$(gpg -K | awk '/sec/{getline; print}')
$ BACKUP='path to the backup folder'

```
### Create a revocation certificate

Now that you have the master key, it’s good practice to create a revocation certificate. If you ever lose your PGP key, or forget the passphrase, you can use publish the revocation certificate to inform others that your key is no longer in use.

```bash
# gpg --gen-revoke $KEY_ID > $BACKUP/revocation-certificate.asc

Create a revocation certificate for this key? (y/N) y
Please select the reason for the revocation:         
  0 = No reason specified
  1 = Key has been compromised
  2 = Key is superseded
  3 = Key is no longer used
  Q = Cancel
(Probably you want to select 1 here)
Your decision? 3
Enter an optional description; end it with an empty line:
> Using revocation certificate that was generated when key B8EFD59D was
> first created.  It is very likely that I have lost access to the
> private key.
> 
Reason for revocation: Key is no longer used
Using revocation certificate that was generated when key B8EFD59D was
first created.  It is very likely that I have lost access to the
private key.
Is this okay? (y/N) y
                     
ASCII armored output forced.
Revocation certificate created.

Please move it to a medium which you can hide away; if Mallory gets
access to this certificate he can use it to make your key unusable.
It is smart to print this certificate and store it away, just in case
your media become unreadable.  But have some caution:  The print system of
your machine might store the data and make it available to others!
```

### Set up strong hash and encryption algorithms
 
```bash
# Set GnuPG to prefer strong hash and encryption algorithms
echo "cert-digest-algo SHA512" >> .gnupg/gpg.conf
echo "default-preference-list SHA512 SHA384 SHA256 SHA224 AES256 AES192 AES CAST5 ZLIB BZIP2 ZIP Uncompressed" >> .gnupg/gpg.conf
```

### Make a backup of the secret keys
The remaining keys will be generated directly on the Yubikey. Importing the encryption key into the Yubikey is a destructive process. It will remove the secret key from the GnuPG keyring. This is a good time to make a backup of the secret keys.
```
$ gpg --export-secret-key $KEY_ID > $BACKUP/secret.pgp
```

I like to delete the GnuPG secret key and reimport it from a backup just to be sure that the export worked.

```bash
$ gpg --delete-secret-key $KEY_ID
$ gpg --import < $BACKUP/secret.pgp
```

### Generate the signing and authentication subkeys
The subkeys for signing and authentication will be unique for each Yubikey. This allows the subkeys to be generated directly on the Yubikey, where the private key cannot be accessed from the computer.
```
$ gpg --edit-key $KEY_ID
gpg> addcardkey

 Signature key ....: [none]
 Encryption key....: [none]
 Authentication key: [none]
 
 Please select the type of key to generate:
    (1) Signature key
    (2) Encryption key
    (3) Authentication key
 Your selection? 1
 
 
Please specify how long the key should be valid.
         0 = key does not expire
        = key expires in n days
      w = key expires in n weeks
      m = key expires in n months
      y = key expires in n years
Key is valid for? (0) 10y
Key expires at Fri Jan  1 22:08:14 2028 PST
Is this correct? (y/N) y
Really create? (y/N) y  

pub  3072R/B8EFD59D  created: 2018-01-02  expires: 2016-01-02  usage: C   
                     trust: ultimate      validity: ultimate
sub  2048R/EE86E896  created: 2018-01-02  expires: 2016-01-02  usage: E   
sub  2048R/79BF574F  created: 2018-01-02  expires: 2016-01-02  usage: S 
[ultimate] (1). Daniel Törbacka (Taco) <daniel.torbacka@gmail.com> 

# Do the same for the authentication key
gpg> addcardkey

 Signature key ....: 546D 6A7E EB4B 5B07 B3EA  7373 12E2 68AD 79BF 574F
 Encryption key....: [none]
 Authentication key: [none]
 
 Please select the type of key to generate:
    (1) Signature key
    (2) Encryption key
    (3) Authentication key
 Your selection? 3
                  
 Please specify how long the key should be valid.
          0 = key does not expire
         = key expires in n days
       w = key expires in n weeks
       m = key expires in n months
       y = key expires in n years
 Key is valid for? (0) 10y
 Key expires at Fri Jan  1 22:09:41 2028 PST
 Is this correct? (y/N) y
 Really create? (y/N) y  
                       
 pub  3072R/B8EFD59D  created: 2018-01-02  expires: 2016-01-02  usage: C   
                      trust: ultimate      validity: ultimate
 sub  2048R/EE86E896  created: 2018-01-02  expires: 2016-01-02  usage: E   
 sub  2048R/79BF574F  created: 2018-01-02  expires: 2016-01-02  usage: S   
 sub  2048R/934AE2EE  created: 2018-01-02  expires: 2016-01-02  usage: A  
 [ultimate] (1). Daniel Törbacka (Taco) <daniel.torbacka@gmail.com> 
 
 # Use toggle and key to select the private encryption key
gpg> toggle
gpg> key 1

sec  3072R/B8EFD59D  created: 2018-01-02  expires: 2016-01-02
ssb* 2048R/EE86E896  created: 2018-01-02  expires: never     
ssb  2048R/79BF574F  created: 2018-01-02  expires: 2016-01-02
                     card-no: 0006 12345678
ssb  2048R/934AE2EE  created: 2018-01-02  expires: 2016-01-02
                     card-no: 0006 12345678
(1). Daniel Törbacka (Taco) <daniel.torbacka@gmail.com> 

# Then move the encryption key from the GnuPG keyring to the Yubikey
gpg> keytocard
 Signature key ....: 546D 6A7E EB4B 5B07 B3EA  7373 12E2 68AD 79BF 574F
 Encryption key....: [none]
 Authentication key: DCE4 7FEA 4A72 E525 681C  6207 662E 5CA8 934A E2EE

Please select where to store the key:
   (2) Encryption key
Your selection? 2
                 
sec  3072R/B8EFD59D  created: 2015-01-02  expires: 2016-01-02
ssb* 2048R/EE86E896  created: 2015-01-02  expires: never     
                     card-no: 0006 12345678
ssb  2048R/79BF574F  created: 2015-01-02  expires: 2016-01-02
                     card-no: 0006 12345678
ssb  2048R/934AE2EE  created: 2015-01-02  expires: 2016-01-02
                     card-no: 0006 12345678
(1). Daniel Törbacka (Taco) <daniel.torbacka@gmail.com> 
                     
gpg> save
```
### Save and Distribute the public OpenPGP key

Save and Distribute the public OpenPGP key
When the master key was created, and each time a subkey was created, a public and private RSA key was also generated. The private keys should remain on Yubikey and backup media.

The public keys should be distributed to a location where others can find it. One example is [pgp.mit.edu](http://pgp.mit.edu).

Once a location has been chosen, it’s a good idea to embed the location into the PGP key. That way users know where to find the version of the key with the most up-to-date signatures, subkeys, and revocations. GnuPG can also automatically fetch the latest version of the key with –refresh-keys if the location is embedded within the key. The keyserver command embeds a URL to this key within the public PGP key.

```
# Export pulbic keys
$ gpg --armor --export $KEY_ID > $BACKUP/public.asc
$ cat $BACKUP/public.asc
# Copy the output
```
Upload the pubkey here [pgp.mit.edu](http://pgp.mit.edu)

```bash
gpg --edit-key $KEY_ID

gpg> keyserver
Enter your preferred keyserver URL: http://pgp.mit.edu/pks/lookup?op=get&search=daniel.torbacka@gmail.com

gpg> showpref
# Check so info is correct

gpg> save
```
### Update the Yubikey

The next step is to change the Yubikey PINs and import the public key.

```
$ gpg --card-edit

gpg/card> admin
Admin commands are allowed

# Change the PIN and Admin PINs
gpg/card> passwd
gpg: OpenPGP card no. D2760001240102000006123456780000 detected

1 - change PIN
2 - unblock PIN
3 - change Admin PIN
4 - set the Reset Code
Q - quit

Your selection? 1

1 - change PIN
2 - unblock PIN
3 - change Admin PIN
4 - set the Reset Code
Q - quit

Your selection? 3
PIN changed.     

1 - change PIN
2 - unblock PIN
3 - change Admin PIN
4 - set the Reset Code
Q - quit

Your selection? q

# Make sure the PIN is entered before signing (optional)
gpg/card> forcesig

# Set the URL where the OpenPGP public key can be found.
gpg/card> url

URL to retrieve public key: http://pgp.mit.edu/pks/lookup?op=get&search=daniel.torbacka@gmail.com

# Fetch the public key into the local keyring
gpg/card> fetch

gpg/card> quit

# Finally, populate the secret keyring with stub keys that point to the Yubikey
> gpg --card-status
```

### Use gpg authentication for ssh

[Documentation for setup](https://wiki.archlinux.org/index.php/GnuPG#SSH_agent)

```
# After you should be able to see your gpg authentication pulbic key in ssh format 
$ ssh-add -L
ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQCbeMeTeSEZM2NTv6LueXco0uj4puj5p2FHQBWZYu56xJ+QzHUhq546MnywzlsHDpG7EOECBQnWnscLXFjoY0wSXQx5ejHnJVK4+iziCSoF2GO0h9+3B3HTu1IzgVQgSt/37r2/cLmXbamOxOCSTXKDM6ynyW20b1i6vB+eokyTnF40In1AoapnwpaA0ssLVLiua/try8uQ0+OqTep7cg41/2MQN+6bN1y6BoK0O2H8l7bl5mh1uuGa8+OQ0wbsPk6ZISjcylWImeCwNcK0PKF58MPMUgCTYbK4dgGnj8QK1b4HqwL+v784+kXEWI1vDMxl+I0fTKHgBydl43Ojzldz cardno:000607599367
```
Copy that to github and gitlab. Also add it to all servers you want access to.
