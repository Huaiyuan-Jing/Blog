---
title: How to manage email like an Advanced-Linux-User
date: 2025-09-4
tags:
  - Security
  - Linux
index_img: https://github.com/user-attachments/assets/043d0a6d-9fb4-44f4-8664-d943b8347226
banner_img: https://github.com/user-attachments/assets/043d0a6d-9fb4-44f4-8664-d943b8347226
abbrlink: 93ebbc6b
---

## Introduction: Why?

It seems you're describing a world many of us can only dream of – one free from the daily deluge of digital communication. Imagine the luxury of a life unburdened by countless logins, where "inbox zero" isn't a mythical achievement but the natural state of being.

For the rest of us, however, reality is a relentless stream of notifications. We navigate a labyrinth of web pages, each demanding our attention, or wrestle with email clients like Outlook and Thunderbird – digital behemoths brimming with features we'll never touch, yet somehow managing to monopolize half our screens. Lucky indeed are those who escape this modern-day communication treadmill.

But luckily, you are a linux user! Minimalism and Unix Philosophy are your weapons. Now pick them up and see how deep the rabbit hole can be.

## Aerc: Unix-Philosophy Driven Email Client

![](/1.png)

1. No dependency on GUI
2. No messy folder management
3. No any other unnecessary and uncompatible features
4. Elegent Plain Text Workflow

### How to setup Aerc? (Based on Ubuntu 24.04 LTS Server)

1. Install aerc:

```bash
sudo apt update && apt install aerc
```

1. Start to configure:

```bash
aerc
```

![](/2.png)

Input your account information sequantially. And press `Next` to the next step.

![](/3.png)

Here I setup my google account. Input your email address in `Username`. About `Password`, if you are using Personal Google Account or other personal email service (like posteo I used), you can create an APP PASSWORD for Aerc. 

For those who use School Email or Company Email, or just don't want to use app password because they are not safe, I will introduce how to set it up later with a different tool.

Leave everything else as default, press `Next` and do the almost same setup for outgoing mail.

Then you are done with the basic setup.

You can change your setting with the following command:

```bash
vim ~/.config/aerc/account.conf
```

## Oama: Local Oauth2 Authenticator

You might notice that with the previous way to setup, most of school / company email services cannot be used because they enforce Oauth2 authentication. Though aerc support to use OAuth2 token, it cannot send a requet to get it by itself. 

So here comes Oama, a local Oauth2 Authenticator written in Haskell. It can dynamically fetch the token from your Google account and store it locally.

### Setup Oama and Aerc

1. Install Oama:

Cause most Linux distributions do not have Oama in their official repo, you need to install its binary manually.

[Oama Github Link](https://github.com/pdobsan/oama/releases)

Fetch the newest version of Oama and extract it. And add the binary file to the System PATH.

2. Get Oauth2 Token:

Run Oama:

```bash
$ oama
WARNING -- Could not find config file: /home/<USERNAME>/.config/oama/config.yaml
Creating initial config file ...
... done.
Edit it then start oama again.
```

Open config file:

```bash
$ vim ~/.config/oama/config.yaml
```

```yaml
## oama version 0.21.0
## This is a YAML configuration file, indentation matters.
## Double ## indicates comments while single # default values.
## Not all defaults are shown, for full list run `oama printenv`
## and look at the `services:` section.

## Possible options for keeping refresh and access tokens:
## GPG - in a gpg encrypted file $XDG_STATE_HOME/oama/<email-address>.oauth
##       (XDG_STATE_HOME defaults to ~/.local/state)
## GPG - in a gpg encrypted file ~/.local/state/oama/<email-address>.oauth
## KEYRING - in the keyring of a password manager with Secret Service API
##
## Choose exactly one.

encryption:
    tag: KEYRING

# encryption:
#   tag: GPG
#   contents: your-KEY-ID

## Builtin service providers
## - google
## - microsoft
## Required fields: client_id, client_secret
##
services:
  google:
    client_id: application-CLIENT-ID
    client_secret: application-CLIENT-SECRET
  ## Alternatively get them from a password manager using a shell command.
  ## If both variants are present then the _cmd versions get the priority.
  ## For example:
  # client_id_cmd: |
  #   pass email/my-app | head -1
  # client_secret_cmd: |
  #   pass email/my-app | head -2 | tail -1
  #  auth_scope: https://mail.google.com/

  microsoft:
     client_id: application-CLIENT-ID
  ## client_secret is not needed for device code flow
  #  auth_endpoint: https://login.microsoftonline.com/common/oauth2/v2.0/devicecode
  ##
  ## client_secret might be needed for other authorization flows
  #  client_secret: application-CLIENT_SECRET
  ## auth_endpoint: https://login.microsoftonline.com/common/oauth2/v2.0/authorize
  #
  #  auth_scope: https://outlook.office.com/IMAP.AccessAsUser.All
  #     https://outlook.office.com/SMTP.Send
  #     offline_access
  #  tenant: common

  ## User configured providers
  ## Required fields: client_id, client_secret, auth_endpoint, auth_scope, token_endpoint
  ##
  ## For example:
  # yahoo:
  #   client_id: application-CLIENT-ID
  #   client_id_cmd: |
  #     password manager command ...
  #   client_secret: application-CLIENT_SECRET
  #   client_secret_cmd: |
  #     password manager command ...
  #   auth_endpoint: EDIT-ME!
  #   auth_scope: EDIT-ME!
  #   token_endpoint: EDIT-ME!
```

Here we don't need to care about the other parts, only change `client_id` with `9e5f94bc-e8a4-4e73-b8be-63364c29d753`. This is ID of Thunderbird. You can also change `tag`
setting to use GPG encryption like this: 

```yaml
encryption:
    tag: GPG
    contents: <Your-GPG-key-id>
```

After change these settings, goes back to terminal and run:

```bash
$ oama authorize microsoft <your-email-addr> --device
Visit:
https://microsoft.com/devicelogin

and enter the code:
XXXXXXXXXXXX
```

Follow the webpage guidence to finish the setup on oama. Finally we open Aerc config file and setup the email account:

```bash
vim ~/.config/aerc/accounts.conf
```

```config
[<Name of Email Account>]
source            = imaps+xoauth2://<Your-Email-Addr>@<IMAP-Addr>
source-cred-cmd   = "oama access <Your-Email-Addr>"
outgoing          = smtp+xoauth2://<Your-Email-Addr>@<SMTP-Addr>
outgoing-cred-cmd = "oama access <Your-Email-Addr>"
default           = INBOX
cache-headers     = true
from              = "<Your-Name>" <Your-Email-Addr>
check-mail        = 1m # this refreshes the email every 1 minutes
folders           = INBOX, Sent, Archive, Trash
```

Save and close the file. Now run Aerc and manage your emails like an advanced Linuxuser. Enjoy! 🚀