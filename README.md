# Using git-secret to safely store confidential data in your git repository

## Common remarks

[git-secret](https://git-secret.io/) uses [gpg](https://gnupg.org/) to encrypt confidential data in [git](https://git-scm.com/) repositories.

## Preparations

Before you can use git-secret you must create a RSA key pair. Check out README-gpg.md hot to do that.

Once you have a RSA key pair you can initialize a new and empty or an existing git repository with `git secret init`