---
layout: post
title: Multiple SSH Support
date: 2021-05-06 23:57 +0545
categories: git
tags: git
---

## SSH Commands

``` shell
# restart ssh-agent
eval $(ssh-agent -s)

# remove all registered ssh keys
ssh-add -D

# list registered keys
ssh-add -l

# generate ssh key
ssh-keygen -t ed25519 -C "ashim.jung.khadka@gmail.com"         
```

## Register ssh private key

Although its automatically registerd but in case if needed

``` shell
ssh-add ~/.ssh/ashimjk_id_ed25519

ssh-add ~/.ssh/ajk_hub_id_ed25519

ssh-add ~/.ssh/clusus_id_ed25519

ssh-add ~/.ssh/bucket_id_ed25519
```

## Git Config

Create config file in ~/.ssh/config and add following config as per needed

``` 

# AshimJK GitHub account
Host github.com
 HostName github.com
 User ashimjk
 IdentityFile ~/.ssh/ashimjk_id_ed25519

# AJK-Hub GitHub account
Host github.com-ajk-hub
 HostName github.com
 User ajk-hub
 IdentityFile ~/.ssh/ajk_hub_id_ed25519

# Gitlab account
Host gitlab.com
 HostName gitlab.com
 User ps.ashim.khadka
 IdentityFile ~/.ssh/clusus_id_ed25519

# Bitbucket account
Host bitbucket.org
 HostName bitbucket.org
 User ashimjk
 IdentityFile ~/.ssh/bucket_id_ed25519
```

## For Testing

``` shell
git clone git@github.com:ashimjk/py-sdk.git

git clone git@github.com-ajk-hub:ajk-hub/testing.git

git clone git@gitlab.com:progressoft/kryptonite/corpay-ui/system-config-ui.git

git clone git@bitbucket.org:ashimjk/sdk-tool.git
```

## References

* [SSH Config Docs](https://linux.die.net/man/5/ssh_config)

* [Use multiple ssh-keys](https://xiaolishen.medium.com/use-multiple-ssh-keys-for-different-github-accounts-on-the-same-computer-7d7103ca8693)

* [Manage SSH Keys](https://www.freecodecamp.org/news/how-to-manage-multiple-ssh-keys/)
