---
layout: post
title: Manage Multiple SSH Keys
date: 2021-05-06 23:57 +0545
categories: git
tags: git
---

## Scenario

I have multiple SSH keys, want to manage them without any trouble.
I can do it in various ways:

1. creating a directory for ssh key and switch every time I need to or even create a script for that.
2. use the same ssh key for all accounts like Github, Bitbucket, Gitlab, etc. But there is one catch, which is
I cannot use the same ssh key on the same server like Github.
4. Create a separate ssh key for all accounts and manage it using ssh config, which I am going to use in this post.

## Assumptions

1. You have basic knowledge about Git and SSH.
2. All your generated ssh keys will be inside the `~/.ssh` directory.

## Step to create ssh config

1. Generate ssh key for your account. The following command will generate private and public keys.
``` shell
ssh-keygen -t ed25519 -C "<EMAIL_ADDRESS>"
```

2. Rename your ssh key and give it some meaningful name like in my case:
``` shell
mv ~/.ssh/id_ed25519 ~/.ssh/github_id_ed25519
```

3. Repeat 1 & 2 Steps for all your accounts

4. Create `config` file inside `.ssh` directory
``` shell
touch ~/.ssh/config
```

5. Add the following configuration inside your `config` file. You can add/remove based on your needs:

    ``` config
    # GitHub Account 1
    Host github.com
    HostName github.com
    User <USER_NAME_1>
    IdentityFile ~/.ssh/github_id_ed25519

    # Github Account 2
    Host github.com-<USER_NAME_2>
    HostName github.com
    User <USER_NAME_2>
    IdentityFile ~/.ssh/github2_id_ed25519

    # Gitlab Account
    Host gitlab.com
    HostName gitlab.com
    User <USER_NAME>
    IdentityFile ~/.ssh/gitlab_id_ed25519

    # Bitbucket Account
    Host bitbucket.org
    HostName bitbucket.org
    User <USER_NAME>
    IdentityFile ~/.ssh/bucket_id_ed25519
    ```

### Note:

For the Github account, we have defined two accounts.

* One with default HostName
* Another with extra content append to default HostName, which is `USERNAME_2`

Also, note that I have added `dash (-)` after the HostName.

## How to use it

It's simple. Just do what you were doing with a single SSH key for all accounts including GitHub Account 1.

But for GitHub Account 2, we have to handle it differently.

Let's say if we have a URL like this `git@github.com:<USER_NAME_2>/project.git`

then we need to append git@github.com `-<USER_NAME_2>` :<USER_NAME_2>/project.git.
It's the only thing that we have to do before we use it.

``` shell
# For Github Account 2
# Instead of this
git clone git@github.com:<USER_NAME_2>/project.git

# We have to use it in this way
git clone git@github.com-<USER_NAME_2>:<USER_NAME_2>/project.git
```

## Some useful SSH command

* If your ssh-agent failed then you can restart it using the following command
``` shell
eval $(ssh-agent -s)
```
* If you want to list all registered keys
``` shell
ssh-add -l
```
* If you want to clear all registered keys
``` shell
ssh-add -D
```
