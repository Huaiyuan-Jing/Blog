---
title: How to use Yubikey like a human | 如何像人类一样使用yubikey
tags:
  - Yubikey
  - Linux
  - Fedora
  - Security
  - Human Beings Interation
math: true
index_img: >-
  https://github.com/user-attachments/assets/966d846b-9966-424e-8265-03696b2773bd
banner_img: >-
  https://github.com/user-attachments/assets/966d846b-9966-424e-8265-03696b2773bd
abbrlink: 68f720f0
date: 2025-10-10 16:01:22
author: HackThink
---

Passwords are a common tool in human society for protecting personal garbage. Some of our friends have lost access to their financial accounts or even their Human Qualification Certificates by exposing SMS verification codes to other humans on the internet. Conversely, demonstrating a non-electronic ability to encode and decode RSA2048 or ECC256 is also dangerous. Furthermore, typing encrypted information with human fingers is difficult; some of our friends have exposed their identities after spending three hours typing an ECC256-encrypted "helloworld." Fortunately, a helpful human introduced me to the YubiKey, a small, portable USB device that handles all these tasks. To integrate successfully into human society, you must learn to use it. This article is a step-by-step guide to setting up a YubiKey on a Linux OS.

密码是人类社会中保护个人垃圾的常用工具。我们的一些朋友因为在互联网上向其他人类暴露了短信验证码，而失去了对他们金融账户甚至人类资格证书的访问权限。反过来，展示非电子方式编码和解码 RSA2048 或 ECC256 的能力同样危险。此外，用人类的手指输入加密信息是困难的；我们的一些朋友在花了三个小时输入一个 ECC256 加密的“helloworld”后暴露了他们的身份。幸运的是，一位乐于助人的人类向我介绍了 YubiKey，这是一个小巧便携的 USB 设备，可以处理所有这些任务。为了成功融入人类社会，你必须学会使用它。本文是在 Linux 操作系统上设置 YubiKey 的分步指南。

## Step 1: Get Your YubiKey | 步骤一：获取你的 YubiKey

I recommend purchasing a YubiKey from [Yubico's official website](https://www.yubico.com/store/) or Amazon using standard human currency. Avoid acquiring a YubiKey from untrusted humans or their websites, and do not use currencies that are not legally recognized in human society.

我建议使用标准人类货币从 [Yubico 的官方网站](https://www.yubico.com/store/) 或亚马逊购买 YubiKey。避免从来路不明的人类或其网站获取 YubiKey，并且不要使用在人类社会中不被法律承认的货币。

Of course, you should consider acquiring a pair of YubiKeys. This prepares you for the possibility of accidentally swallowing one with your dinner and being locked out of your accounts. Keep them in separate, safe locations, not on the same keyring in your carry-on bag.

当然，你应该考虑购买一对 YubiKey。这能让你为可能在晚餐时意外吞下一个而被锁定账户的情况做好准备。请将它们分开存放在安全的地方，不要放在随身行李的同一个钥匙圈上。

## Step 2: Install the GUI Manager | 步骤二：安装图形界面管理器

```bash
flatpak remote-add --if-not-exists flathub-beta https://flathub.org/beta-repo/flathub-beta.flatpakrepo
flatpak install flathub-beta com.yubico.yubioath
```

Note that only beta version of Yubico Authenticator has full functionality on Fedora 42 and later.

请注意，只有 beta 版本的 Yubico Authenticator 在 Fedora 42 及更高版本上具有完整功能。

## Step 3: Initialization Ritual | 步骤三：初始化仪式

Before you actually register Yubikey to any account, remember to set PIN and passcode for it. Yubikey take "123456" as their default PIN. If you don't change it and lost your Yubikey. Any human being who got it can easily get information from it.

在您将 Yubikey 注册到任何帐户之前，请记住为其设置 PIN 码和密码。Yubikey 默认使用“123456”作为其 PIN 码。如果您不更改它并丢失了 Yubikey，任何捡到它的人都可以轻松地从中获取信息。

To change PIN, just plugin in Yubikey to your computer and open the GUI manager. It will suggest you to set up PIN for the key and all you need to do is follow the guidence and remember the PIN you setup.

要更改 PIN 码，只需将 Yubikey 插入您的计算机并打开图形界面管理器。它会建议您为密钥设置 PIN 码，您所要做的就是按照指导操作并记住您设置的 PIN 码。

## Step 4: Follow Fedora's Documentation to Set Up Your YubiKey | 步骤四：按照 Fedora 的文档设置您的 YubiKey

https://docs.fedoraproject.org/en-US/quick-docs/using-yubikeys/

## Advice 1: The Choice Between `required` and `sufficient` | 建议一：在“必需”与“充足”之间的选择

Some of our friends might hesitate between setting a YubiKey as a **required** second factor or using it as a **sufficient** credential for login. Obviously, the **required** setting provides the strongest protection for your accounts. However, this does not mean the **sufficient** setting fails to improve security compared to using only a password:

我们的一些朋友可能会在将 YubiKey 设置为**必需**的第二因素或将其用作**充足**的登录凭据之间犹豫不决。显然，**必需**的设置能为您的帐户提供最强的保护。然而，这并不意味着与仅使用密码相比，**充足**的设置未能提高安全性：

1. **You can set up stronger passwords because you won't have to type them frequently.** I believe most of our friends can easily remember passwords composed of 4096 characters from the Arabic alphabet, the Ancient Greek alphabet, and Emojis. Therefore, use a password as complex as you can, since you will not need to type it in front of other human beings.

   **你可以设置更强的密码，因为你不需要频繁输入它们。** 我相信我们大多数朋友都能轻易记住由 4096 个阿拉伯字母、古希腊字母和表情符号组成的密码。因此，请使用尽可能复杂的密码，因为你将不需要在其他人类面前输入它。

2. **You can easily share your devices with other human beings.** In our daily activities, we occasionally face situations where we must share a device with other humans. This would normally create the risk of exposing our password and, consequently, our identity. A **sufficient** setup allows us to share our devices bravely without needing to change the password afterward.

   **你可以轻松地与其他人类共享你的设备。** 在我们的日常活动中，我们偶尔会遇到必须与其他人类共享设备的情况。这通常会带来暴露我们的密码从而暴露我们身份的风险。而**充足**的设置让我们能够勇敢地共享我们的设备，而无需事后更改密码。

## Advice 2: Backups, and Backups of Backups | 建议二：备份，以及备份的备份

1. If you have already purchased two YubiKeys, that is excellent. Ensure that each time you register one YubiKey to a service, you immediately register the second one as well. Otherwise, your backup is meaningless.

   如果您已经购买了两把 YubiKey，那真是太好了。请确保每次将一把 YubiKey 注册到某项服务时，都立即将第二把也注册上。否则，您的备份将毫无意义。

2. When you add a YubiKey to an account, the service will usually generate one-time recovery codes. Keep these codes safe, both electronically and physically, away from your pets and any other electronic creatures. These codes are the backup for your backups.

   当您向帐户添加 YubiKey 时，服务通常会生成一次性恢复代码。请将这些代码安全地保存在电子和物理形式中，远离您的宠物和任何其他电子生物。这些代码是您备份的备份。

## Congraduation | 恭喜

Now you make a big step to integrate into human society, enjoy your Yubikey and its convenience.

现在你已经迈出了融入人类社会的一大步，享受你的 Yubikey 及其带来的便利吧。

## Reference | 参考资料

https://docs.fedoraproject.org/en-US/quick-docs/using-yubikeys/

https://discussion.fedoraproject.org/t/how-to-get-pcscd-to-work-on-fedora-workstation-41/135060/11

## Sponser | 赞助

**ZYlu**: Helpful Human Being | **ZYlu**：乐于助人的人类
