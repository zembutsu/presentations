
# [2022-03-11] CentOS Linux 8 の EOL と対応策の検討 

## 1. CentOS 8 終了アナウンス

- CentOS Project shifts focus to CentOS Stream – Blog.CentOS.org
  - https://blog.centos.org/2020/12/future-is-centos-stream/

## 2. 2021年12月31日で CentOS 8 は End of Life (EOL)

### dnf / yum が使えない

centos リポジトリからの提供が終わったため、利用できない。コマンドを実行してもエラーが出る。

```bash
$ dnf update
CentOS Linux 8 - AppStream                                          104  B/s |  38  B     00:00
エラー: repo 'appstream' のメタデータのダウンロードに失敗しました : Cannot prepare internal mirrorlist: No URLs in mirrorlist
```

### /etc/yum.repos.d/ 以下ファイルの書き換えが必要

`mirror.centos.org` を `vault.centos.org` に変更しないと実行できない。

```bash
sed -i -r \
    -e 's!^mirrorlist=!#mirrorlist=!' \
    -e 's!^#?baseurl=http://(mirror|vault).centos.org/\$contentdir/\$releasever/!baseurl=https://dl.rockylinux.org/vault/centos/8.5.2111/!i' \
    /etc/yum.repos.d/CentOS-*.repo
```

その後

```bash
dnf update
```


## 3. CentOS 8 代替ディストリビューション候補

移行用の手順やスクリプトを公開しているもの。

### CentOS Stream 8

- CentOS Stream
  - https://www.centos.org/centos-stream/
- CentOS 8 -> Stream 8 移行手順
  - https://www.centos.org/news-and-events/convert-to-stream-8/

```bash
dnf --disablerepo '*' --enablerepo extras swap centos-linux-repos centos-stream-repos --allowerasing
dnf distro-sync
```

### AlmaLinux

- AlmaLinux OS
  - https://almalinux.org/
- CentOS 8 -> AlmaLinux 移行スクリプト
  - https://github.com/AlmaLinux/almalinux-deploy

移行手順

CentOS 8.4 または CentOS 8.5 が必要

```bash
curl -O https://raw.githubusercontent.com/AlmaLinux/almalinux-deploy/master/almalinux-deploy.sh
sudo bash ./almalinux-deploy.sh
sudo reboot
```

### Rocky Linux

- Rocky Linux
  - https://rockylinux.org/
- Migrating To Rocky Linux - Documentation
  - https://docs.rockylinux.org/guides/migrate2rocky/
- 移行ツール
  - https://github.com/rocky-linux/rocky-tools/
  - https://github.com/rocky-linux/rocky-tools/tree/main/migrate2rocky

```bash
curl https://raw.githubusercontent.com/rocky-linux/rocky-tools/main/migrate2rocky/migrate2rocky.sh -o migrate2rocky.sh
chmod u+x migrate2rocky.sh
./migrate2rocky.sh -r
```

### MIRACLE LINUX

- MIRACLE LINUX
  - https://www.miraclelinux.com/

- https://repo.dist.miraclelinux.net/miraclelinux/migration-tool/


### RHEL

- Convert2RHEL: How to convert from CentOS Linux and Oracle Linux to Red Hat Enterprise Linux
  - https://www.redhat.com/en/technologies/linux-platforms/enterprise-linux/migration-process/convert2rhel-how-to-convert-from-centos-linux-to-red-hat-enterprise-linux


### Oracle Linux

- Oracle Linuxに切替え
  - https://docs.oracle.com/ja/solutions/migrate-centos-ora-linux/switch-oracle-linux1.html
- oracle/centos2ol: Script and documentation to switch CentOS Linux to Oracle Linux
  - https://github.com/oracle/centos2ol

