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

## Encrypting secrets

- Initialize the git-secret repository by running `git secret init`. It will do the following things:
    - `.gitsecret/` folder will be created, with subdirectories `keys/` and `paths/` 
    - `.gitsecret/keys/random_seed` will be added to `.gitignore`
    - `.gitignore` will be configured to <u>not ignore</u> `.secret` files.

> :pushpin: All the contents of the `.gitsecret/` folder should be checked in, `/except/` the `random_seed` file. This also means that of all the files in `.gitsecret/`, only the random_seed file should be mentioned in your `.gitignore` file.

> :information_source: This is needed only once.

- Add the first user to the git-secret repo keyring by running `git secret tell <your@email.id>`

- Add files you wish to encrypt inside the git-secret repository. This can be done by running `git secret add <filenames...>` command, which will also (as of 0.2.6) add entries to `.gitignore`, stopping those files from being added or committed to the repo unencrypted.

- Then run `git secret hide` to encrypt the files you added with `git secret add`. The files will be encrypted with the public keys in your git-secret repo’s keyring, each corresponding to a user’s email that you used with tell.

- After using git secret hide to encrypt your data, it is safe to commit your changes. 
> :information_source: It is recommended to add the `git secret hide` command to your pre-commit hook.

## Decrypting secrets

You can decrypt files with the `git secret reveal` command. 
> :pushpin: Print their contents to `stdout` with the `git secret cat` command. 

> :warning: If you used a password on your GPG key (always recommended), it will ask you for your password.

## Add someone else to the repository

- Get their `gpg public-key` (secret key not needed). For exporting, refer above.

- Import this key into your gpg keyring (in ~/.gnupg or similar). Refer above for the command.

- Add this person to your secrets repo by running `git secret tell their@email.id` (this will be the email address associated with their public key)

- Remove the other user’s public key from your personal keyring with `gpg --delete-keys their@email.id`

- Re-encrypt the files using:
```sh
git secret reveal 
git secret hide -d
```
Commit and push the newly encrypted files. Now the newly added user will be able to decrypt the files in the repo using `git secret reveal`.

> :information_source: The `-d` option deletes the unencrypted file after re-encrypting it