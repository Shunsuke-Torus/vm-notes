## VM実験環境概要

使用する機材の詳細な構成は[こちら](00-VM-Machines.md)を参照してください
- ネットワーク空間は`192.168.100.0/24`
- ドメインは`nlab.local`

| PC Number | ホスト名 | IPアドレス |
| ---- | ---- | ---- |
| PC1 | esxi1 | 192.168.100.11/24 |
| PC2 | esxi2 | 192.168.100.12/24 |
| PC3 | esxi3 | 192.168.100.13/24 |
| PC4 | esxi4 | 192.168.100.14/24 |
| ---- | vcenter | 192.168.100.21/24 |

DNSを用意せずに構築するため、vCenter構築段階で以下の設定を導入した

- ステージ1  
FQDN: `192.168.100.21`  
デフォルトゲートウェイ: `192.168.100.1`  
DNSサーバ: `192.168.100.1`

- ステージ2  
1. SSHを有効にしたあと、vCenterにSSH接続を行う
2. hostファイルを編集する  
```
# vi /etc/hosts
以下の行を追加した

192.168.100.11 esxi1 esxi1.nlab.local
192.168.100.12 esxi2 esxi2.nlab.local
192.168.100.13 esxi3 esxi3.nlab.local
192.168.100.14 esxi4 esxi4.nlab.local

192.168.100.21 localhost
192.168.100.21 vcenter vcenter.nlab.local
```

3. vCenter内部のdnsmasqの設定を変更する
```
# vi /etc/dnsmasq.conf

no-hosts -> #no-hosts（コメントアウト）

no-resolv（追記）
bogus-priv（追記）
```
- `no-hosts`をコメントアウトして`/etc/hosts`での名前解決を許可
- `no-resolv`を追記して`/etc/resolv.conf`を無視する
- `bogus-priv`を追記してプライベートIPアドレスの上位DNSへの逆引きを禁止する

4. dnsmasqを再起動
```
# systemctl restart dnsmasq.service
```

5. インストールを続行
