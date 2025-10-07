---
title: Auto-Snapshot with dnf on Fedora | 在fedora上使用dnf自动快照
tags:
  - Linux
  - Fedora
math: true
date: 2025-10-06 20:50:38
index_img: https://github.com/user-attachments/assets/ab9d5c56-4802-4ff3-9342-2fabd4b665e6
banner_img: https://github.com/user-attachments/assets/ab9d5c56-4802-4ff3-9342-2fabd4b665e6
---

When I was using Arch Linux, one of my favorite packages was `snap-pac`, which works elegantly with `pacman` to create snapshots before and after any update.

当我在用 Arch Linux 的时候，我最喜欢的软件包之一是 `snap-pac`，它可以和 `pacman` 优雅地配合，在任何更新前后创建快照。

After I returned to Fedora, I wanted to implement a similar process for `dnf`. However, I found the solution is a little more complicated than on Arch, especially for `dnf5`. So, this article is a step-by-step guide to set it up.

回到 Fedora 后，我想为 `dnf` 实现类似的功能。然而，我发现解决方案比 Arch 上要复杂一些，特别是对于 `dnf5`。所以，这篇文章是一个手把手的设置指南。

## 1. Install necessary packages | 安装必要的软件包

```bash
sudo dnf install python3-dnf-plugin-snapper libdnf5-plugin-actions snapper -y
```

## 2. Setup snapper config | 设置 snapper 配置

```bash
sudo snapper -c root create-config /
```

If you want, you can also create snapshot for /home as well:

如果你愿意，也可以为 /home 创建快照：

```bash
 sudo snapper -c home create-config /home
```

## 3. Setup dnf-snapper | 设置 dnf-snapper

```bash
 sudo nano /etc/dnf/libdnf5-plugins/actions.d/snapper.actions
```

And copy and paste the following content:

然后复制并粘贴以下内容：

```
# Get the snapshot description
pre_transaction::::/usr/bin/sh -c echo\ "tmp.cmd=$(ps\ -o\ command\ --no-headers\ -p\ '${pid}')"

# Creates pre snapshots for root and home and stores snapshot numbers in variables
pre_transaction::::/usr/bin/sh -c echo\ "tmp.snapper_pre_root=$(snapper\ -c\ root\ create\ -c\ number\ -t\ pre\ -p\ -d\ '${tmp.cmd}')"
pre_transaction::::/usr/bin/sh -c echo\ "tmp.snapper_pre_home=$(snapper\ -c\ home\ create\ -c\ number\ -t\ pre\ -p\ -d\ '${tmp.cmd}')"

# Creates post snapshots for root and home if pre snapshot numbers exist
post_transaction::::/usr/bin/sh -c [\ -n\ "${tmp.snapper_pre_root}"\ ]\ &&\ snapper\ -c\ root\ create\ -c\ number\ -t\ post\ --pre-number\ "${tmp.snapper_pre_root}"\ -d\ "${tmp.cmd}"
post_transaction::::/usr/bin/sh -c [\ -n\ "${tmp.snapper_pre_home}"\ ]\ &&\ snapper\ -c\ home\ create\ -c\ number\ -t\ post\ --pre-number\ "${tmp.snapper_pre_home}"\ -d\ "${tmp.cmd}"
```

Now save and exit, and restart the computer if you can. Everything should work properly now.

现在保存并退出，如果可以的话重启电脑。一切都应该能正常工作了。

## Conclusion | 总结

Using linux OS is an infinite loop of crash and recover. And snapshot is the only thing can help us take our breath and sleep at night.

使用 Linux 操作系统就是一个无限循环的崩溃和恢复。而快照是唯一能让我们在晚上松一口气，安然入睡的东西。

## Reference | 参考

[Getting snapper/btrfs-assistant to work with dnf5](https://discussion.fedoraproject.org/t/getting-snapper-btrfs-assistant-to-work-with-dnf5/133948/6)
