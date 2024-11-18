# LAN_driver
有線LANアダプタ(NIC)ドライバのインストールについて記述しています。<br>
UbuntuのOSを入れなおしたときにループバックインターフェース(lo)しか認識されていなかったため、
Boot USBを介したパッケージのインストールとNICドライバのインストールを行いました。


## build-essentialパッケージのインストール(makeコマンドが使えない場合)
1. Boot USB(OSインストール用USB)を取り付ける
2. sudo fdisk -l # Boot USBの実行パス(/dev/sd~)を確認
3. sudo mkdir /media/cdrom # ISOファイルのマウント用ディレクトリの作成
4. sudo dd if=/dev/<Boot USBの実行パス> of=ubuntu.iso bs=4M # Boot USBの内容をISOファイルとして保存
5. sudo mount -o loop ubuntu.iso /media/cdrom # ISOファイルをマウント
6. sudo nano /etc/apt/sources.list # ISOからパッケージを取得するように設定
> deb [trusted=yes] file:/media/cdrom focal main restricted # これを加えて他をすべてコメントアウト
7. sudp apt update # ISOファイルからパッケージの取得
8. sudo apt install build-essential # makeに必要となるパッケージのインストール


## NICドライバのインストール
1. lspci | grep ethernet # これでNICのメーカーや型番を確認
2. 適合するNICドライバをダウンロードする(Intelの場合は[こちら](https://www.intel.com/content/www/us/en/download/14611/15817/intel-network-adapter-driver-for-pcie-intel-gigabit-ethernet-network-connections-under-linux-final-release.html)で大体大丈夫です)
3. NICドライバの解凍(Intelの例. tar -xvzf e1000e-3.8.4.tar.gz)
4. インストールディレクトリでターミナルを開く(Intelの例. e1000e-3.8.4/src)
5. sudo make install
6. sudo modprobe -r e1000e
7. sudo modprobe e1000e
8. 正しいものがインストールされていた場合は待機したら有線LANが認識される


## パッケージ取得方法の修正
sudo nano /etc/apt/sources.list
> \# deb [trusted=yes] file:/media/cdrom focal main restricted # 先ほど追加したものをコメントアウト<br>
> deb http://jp.archive.ubuntu.com/ubuntu/ focal main restricted universe multiverse # 追加<br>
> deb http://jp.archive.ubuntu.com/ubuntu/ focal-updates main restricted universe multiverse # 追加<br>
> deb http://jp.archive.ubuntu.com/ubuntu/ focal-backports main restricted # 追加<br>
> deb http://security.ubuntu.com/ubuntu focal-security main restricted universe multiverse # 追加<br>


### 参考文献
- [build-essentialパッケージのインストール](https://anamorphosis.net/tech/apt-install-build-essential%E3%82%92boot-usb%E3%81%8B%E3%82%89%E3%82%AA%E3%83%95%E3%83%A9%E3%82%A4%E3%83%B3%E3%81%A7%E8%A1%8C%E3%81%86%EF%BC%88ubuntu18-04%EF%BC%89/)
- [NICドライバのインストール](https://qiita.com/hatt0519/items/06ac708f08d9570f2b93)
