---
date: 2024-12-14
aliases: linuxでamdgpuを無効化
tags: 
title: linuxでamdgpuを無効化
---
[grub](../DB/Software/Software_DATA/grub.md)の起動オプションを変更することで、amdのGPUを無効化することができます。
1. Grubを起動する
2. 対象エントリを選択した状態で`e`を押して編集モードに入る
3. linuxの行の末尾に`modprobe.blacklist=amdgpu`を追加する
4. `F10`を押して起動する

例
```grub
linux   /boot/vmlinuz ro quiet splash modprobe.blacklist=amdgpu
initrd  /boot/initrd.img-<version>
```