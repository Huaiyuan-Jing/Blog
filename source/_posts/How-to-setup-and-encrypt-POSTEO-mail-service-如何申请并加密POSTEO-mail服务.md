---
title: How to setup and encrypt POSTEO mail service | 如何申请并加密POSTEO mail服务
tags:
  - Security
index_img: https://github.com/user-attachments/assets/be328284-1710-4a7d-bacc-127dd03b384f
banner_img: https://github.com/user-attachments/assets/be328284-1710-4a7d-bacc-127dd03b384f
abbrlink: bd9f93f7
date: 2025-08-31
math:
author: HackThink
---

<!-- @format -->
## Why You Need a Privacy-Focused Email

1. Avoid Over-Centralization and Protect Your Real Identity: Most people currently use a setup consisting of a Gmail account plus a work or school email. Both are strongly tied to their real-world identities. If numerous services are integrated with these accounts, there's a higher risk of exposing personal identity and sensitive information.

2. Data Collection: Most "free" email services collect users' private information and profit by selling this data or using it for targeted advertising. This not only exposes your real information to the provider but also increases the risk of data leaks. A good privacy email service should offer end-to-end encryption, encrypted email content, and multi-factor authentication to prevent anyone — including the provider — from accessing your data.

3. Simplified Management: Most email services that focus on privacy offer aliasing features, allowing users to easily hide their real email addresses and block emails from specific websites directly.

## 为什么需要一个隐私邮箱

1. 避免过度中心化保证真实信息的安全：目前许多人的邮箱配置是一个Gmail邮箱加工作/学校邮箱。这两种邮箱都是与现实身份强挂钩的，如果有大量服务与这两个邮箱集成，真实身份与重要信息暴露的风险越大。

2. 数据收集：大部分的“免费”邮箱服务会对用户的隐私信息进行收集，并通过贩卖信息，投放广告等行为牟利。这不仅让个人真实信息在服务商面前暴露无疑，同时进一步加剧了个人真实信息泄漏的风险。一个良好的隐私邮件服务应当支持全盘加密，邮箱内容加密和多因素认证等功能以防止任何人得到用户的数据，包括服务商自己。

3. 管理简化：大多数以隐私为宣传点的邮箱服务都提供别名服务，可以很方便的隐藏自己的真实邮箱地址，以及直接屏蔽来自某些网站的邮件。

## Why POSTEO mail service? 为什么推荐POSTEO-mail服务？

- Privacy and Security: POSTEO mail service supports the following encryption methods:
  - Public Key Encryption: Emails are encrypted using the user's uploaded PGP public key. This ensures that the service provider cannot see the content (excluding metadata).
  - SSL/TLS Encryption: Provides SSL/TLS encryption to protect privacy during email transmission.
  - Disk Encryption: User data on the server is encrypted via account password, including content and metadata.
  - Two-Factor Authentication & App Passwords: Use two-factor authentication (e.g., two-factor authentication) to reduce the risk after the primary password is leaked. Use app passwords to reduce the risk of primary password leakage.
- Availability:
  - Standard service includes IMAP, POP3, SMTP protocols, and supports various email formats and encodings.
  - Supports calendar and custom aliases (free 2).
- Pricing:
  - Free service: None
  - Standard service: 1 EUR/month

- 隐私安全: POSTEO-mail服务支持的加密方式有以下几种:
  - 公钥加密: 使用用户自己上传的PGP公钥对邮件进行加密。保证邮件对服务商不可见。（不包括元数据）
  - SSL/TLS加密: 提供SSL/TLS加密，保护邮件传输过程中的隐私。
  - 硬盘加密: 对服务器上的用户数据通过账户密码加密，包括内容和元数据。
  - 多因素认证与应用密码: 使用多因素认证（如双因素认证）降低主密码泄露后的威胁。使用应用密码降低主密码泄漏的风险。
- 可用性：
  - 标准服务包含IMAP、POP3、SMTP等协议，支持多种邮件格式和编码。
  - 支持日历，自定义别名（免费2个）。
- 价格：
  - 免费服务：无
  - 标准服务: 1 EUR/month

## How to apply and configure POSTEO-mail service? 如何申请并配置POSTEO-mail服务？

### Step 1: Register for a POSTEO-mail account 第一步：注册一个POSTEO-mail账户

<https://posteo.de/en>

Click sign up button and sign up as any other email service.
按下注册按钮并像其他邮箱一样注册。

### Step 2: Configure MFA 第二步：配置MFA

Login to your POSTEO-mail account and open MFA in password and security page. Don't forget to setup app password at the bottom of the same page if you want to use it on third-party mail client.

登陆到你的POSTEO-mail账户，在密码和安全页面打开MFA。如果你想要使用它在第三方邮件客户端上，请设置应用密码。

### Step 3: Configure Full Disk Encryption 第三步: 配置全盘加密

In the same page, select the "Encryption" section, enter a password, and enable encryption. Note that once enabled, if the user's encryption key is lost, they will be unable to read the email content again, even after resetting the password, so be sure to save the password.

在同样的页面下选择"加密"这一栏, 输入密码并开启加密. 注意这里开启之后如果用户密钥丢失, 将无法再重新读取邮箱里的内容, 即使重置密码也一样, 所以请务必保存好密码.

### Step 4: Configure Inbound Encryption 第四步: 配置入站加密

In the same page, select "PGP Encryption." Here, you need to generate a PGP key locally and then send the public key to the mail server using your own email:

在同样的页面下选择"PGP加密", 这里需要在本地先生成PGP密钥, 再通过自己的邮箱把公钥发送给邮件服务器:

```shell
gpg --full-generate-key
```

When prompted for "Real name," press Enter to skip, or enter your Posteo email address.

在提示输入“Real name”时，直接按回车键跳过，或输入您的 Posteo 邮箱地址。

```shell
gpg --list-keys
gpg --armor --output public-key.asc --export <the id showed in --listh-keys>
```

Finally, send this public key file to <keys@posteo.de> using your own Posteo email.

最后将这个公钥文件通过自己的posteo邮箱发送给<keys@posteo.de>.

## Conclusion 总结

以上步骤分别介绍了如何在Posteo Mail服务中申请并加密POSTEO邮件服务，以及如何配置入站加密。通过这些步骤，你可以成功地在Posteo Mail服务中实现邮件的加密功能，并且可以方便地管理和监控邮件发送和接收情况.
