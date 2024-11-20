---
os:
  - Linux
---
[termux](termux.md)に導入することで、[debian](debian.md)や[ubuntu](ubuntu.md)、[Arch Linux](Arch%20Linux.md)といったLinuxディストリビューションを動作させることができる。

# インストール
```shell
apt update -y
apt install -y proot-distro
```

# ディストリビューションのインストール
例として[debian](debian.md)をインストールしています
```
proot-distro install debian
```
インストールした`proot-distro`環境にログインするには以下のコマンドを実行します
```
proot-distro login debian
```