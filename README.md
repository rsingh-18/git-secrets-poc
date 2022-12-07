# git-secrets-poc
Repository to test working of git secrets.

# Instructions

## Generate keys

- Generate a RSA key-pair:
```sh
gpg --gen-key
```
> :information_source: Install `gnupg` if `gpg` not found

- Export your public key:
```sh
gpg --armor --export <your.email@address.com> > public-key.gpg
```

- Import the public key of someone else (to share the secret with them for instance) (one time / user):
```sh
gpg --import public-key.gpg
```
> :information_source: `public-key.gpg` is the file created in above step which is the public key

## Install git-secret

```sh
git clone https://github.com/sobolevn/git-secret.git git-secret
cd git-secret && make build
PREFIX="/usr/local" make install
```