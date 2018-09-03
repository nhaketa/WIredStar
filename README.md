# スター精密のモバイルプリンタ等を有線接続する
***
![クリエイティブ・コモンズ・ライセンス](https://i.creativecommons.org/l/by/4.0/88x31.png) 本プロジェクトは[クリエイティブ・コモンズ 表示 4.0 国際 ライセンス](http://creativecommons.org/licenses/by/4.0/)の下に提供されています。
***

## 序
Bluetoothで接続するモバイルプリンタは、無線接続の性質上、接続が不安定となる場合がある。
(参考: [タブレットPOS設置 レイアウトの注意](https://www.epson.jp/products/receiptprinter/tabletpos/installation.htm))

[スター精密](https://www.star-m.jp/)の[モバイルプリンタ](https://www.star-m.jp/products/s_print/sp09.html)はどれもBluetooth接続で使用することが前提の作りになっているが、有線で接続するためのコネクタも備えているので、有線接続することで、そのような不安定性を排することができる。

しかし、そのコネクタを使用して有線接続して印刷を行う場合、スター精密が提供するSDKと組み合わせると印刷に支障を生じる場合がある。

以下にその内容と対処方法を記述する。

## Android版SDKを使用する場合

特に問題は生じない。

## iOS版SDKを使用する場合

### iOSに対応した接続形態

モバイルプリンタの有線接続コネクタはシリアル通信(いわゆる'RS-232C')であり、iOSは公式にはシリアル通信をサポートしないため、LAN上に配置したプリンタサーバを経由してシリアル通信を行い、iOSからはネットワークプリンタを使用する形態とする必要がある。(据え置き型プリンタでシリアル接続またはUSB接続を行いたい場合も同様である。)
なお、この接続形態はあくまで論理的なものであり、実際の接続形態はたとえば以下のようにすることができる。

* `iPad等 --- Lightning-USB変換アダプタ -- USB-LANアダプタ -- LAN-シリアル変換器 -- モバイルプリンタ`

あるいは、専用の装置(USB-LANとして認識されるUSB-シリアル変換器)を作成することで、以下の構成とすることも可能である。

* `iPad等 --- Lightning-USB変換アダプタ -- USB-シリアル変換器 -- モバイルプリンタ`

後者の構成の場合、iOSは通常のUSB-シリアル変換ケーブル等をサポートしないため、iOSが公式にサポートしている'USB-LANアダプタ'として認識され、そのアダプタに仮想的に'LAN-シリアル変換器'が接続されるようなUSB装置とする必要がある。（このような構成のUSB装置は容易に実現可能である。）

据え置き型プリンタで、Bluetooth接続やLAN接続ではなくUSB接続を行いたい場合、以下の構成で実現可能である。

* `iPad等 --- Lightning-USB変換アダプタ -- USB-LANアダプタ -- LAN-USBプリンタ変換器 -- 据え置きプリンタ(USB接続)`

専用の装置を作成することで、以下の構成とすることも可能である。

* `iPad等 --- Lightning-USB変換アダプタ -- USB-USBプリンタ変換器 -- 据え置きプリンタ(USB接続)`

こちらの場合も、iOSが通常のUSBプリンタをサポートしないため、iOSが公式にサポートしている'USB-LANアダプタ'として認識され、そのアダプタに仮想的に'LAN-USBプリンタ変換器'が接続されるようなUSB装置とする必要がある。

### iOS版SDKへの対応

このほか、SDK側の制限と思われる、以下の事象に対応する必要がある。（いずれもAndroid版SDKでは発生しないため、iOS版SDKのバグが疑われる。）

* SDKからプリンタへの問い合わせコマンドに対して、プリンタからのレスポンスはTCP/IPの1パケットで返却しなければならない。
* SDKからプリンタへのプリンタバージョン問い合わせコマンドに対して、プリンタからのレスポンスに含まれる機種名が特定の機種でなければならない。現時点で判明している機種名は以下のとおり
  * レスポンスとして認識される機種名
    * SM-T301
  * レスポンスとして認識されない機種名
    * SM-S201i

これらの事象に対応していない場合、プリンタからのレスポンスをSDKが認識せず、プリンタポートを開くことができない。