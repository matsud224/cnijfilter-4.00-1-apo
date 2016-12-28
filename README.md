# cnijfilter-4.00-1-apo
Auto power on patch for Canon PIXUS IJ Printer Driver Ver. 4.00 for Linux

# Overview
Canon PIXUS IJ Printer Driver Ver. 4.00 for Linux を自動電源オン機能に対応させるパッチです。

Canonのプリンタには、印刷ジョブが送信されると自動で電源が入る「自動電源オン機能」を持つものがあります。
地味に便利なのですが、Linux用ドライバではこれが動作しません。
CanonはLinux用ドライバのみソースコードを公開しているため、自動電源オンに対応するよう改良しました。

技術的には、印刷ジョブの冒頭で電源オンのコマンドを送信しているだけです。CUPSが最終的にバックエンド(cnijbe)を呼び、cnijbeはモニタ(cnijlgmon2)を起動します。cnijlgmon2が印刷データのプリンタへの送信を行います。cnijlgmon2がプリンタとのセッションを確立した直後に電源オンのコマンドを送信するように改造しています。

# Install
IJ Printer Driver Ver. 4.00 for Linux がインストールされ、正常に印刷を行えると仮定します。

まず、ドライバのソースコードをCanonのサイトからダウンロードし展開してください。ドライバのダウンロード画面の下部にリンクがあります。本パッチを当て、今回修正したcnijlgmon2というプログラムのみをコンパイルし、既に/usr/bin/に存在しているものを上書きします。

```
$ tar xvf cnijfilter-source-4.00-1.tar.gz
$ patch -p1 -u -d cnijfilter-source-4.00-1 < cnijfilter-4.00-1-apo.diff
$ cd cnijfilter-source-4.00-1/lgmon2
$ ./autogen.sh
$ make
$ sudo cp src/cnijlgmon2 /usr/bin/
```

Ubuntu 16.04, PIXUS MG6730(LAN接続)で動作確認しました。USB接続の場合は未検証です。
MG6730は本来Linux用ドライバが存在しないのですが、IJ Printer Driver Ver. 4.00 for Linuxでうまく動作しています。
同じドライバを使用する機種に PIXUS MG7100,6500,6400,5500,3500,2400,2500 がありますが、動作するか（そもそも自動電源オン機能が付いているか）は不明です。


また、Redhat系OSでは動作確認していませんが、インストール先ディレクトリの違いによりうまく動かないかもしれないと考えています。具体的には、lgmon2/src/cnijlgmon2.cの85行目SetKeyTextDirの引数をハードコードしている箇所と、最後のコピー先ディレクトリを適切に修正すれば大丈夫だと思います。
