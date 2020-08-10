# Chromebook(ARM版)のLinux環境を日本語化してVSCodeを導入する

## 概要

ARM系のChromebook(Lenovo S330)で、Linux環境を日本語化してVSCodeを導入しました。その作業手順を解説します。

## ハードウェア

今回使用するChromebookは次の機種です。

    機種名：Lenovo Chromebook S330
    モニタ：14.0型フルHD液晶(TN)
    CPU：MediaTek MT8173C
    メモリ：4GB
    ストレージ：64GB(eMMC)

## Linux環境の導入

Linux環境は次の作業でインストールします。

    1. 画面右下で時間表示をクリックする
    2. [設定]をクリックする
    3. [Linux（beta）]をクリックし、[有効にする]をクリックする
    4. 画面の指示に従って設定を進める
    5. 最後にターミナルウィンドウが開くので、Linuxコマンドを入力して動作確認する

## Linuxのバージョン

執筆時点(2020/08/10)での「cat /etc/os-release」の実行結果は次の通りです。

    NAME="Debian GNU/Linux"
    VERSION_ID="10"
    VERSION="10 (buster)"
    VERSION_CODENAME=buster
    ID=debian
    HOME_URL="https://www.debian.org/"
    SUPPORT_URL="https://www.debian.org/support"
    BUG_REPORT_URL="https://bugs.debian.org/"REPORT_URL="https://bugs.debian.org/"

## パッケージの更新(update.sh)

    $ sudo apt-get update
    $ sudo apt-get upgrade -y
    $ sudo apt-get dist-upgrade
    $ sudo apt-get autoclean

## GPG keyの導入

sudo apt-get update で次のようなワーニングが表示される場合があります。

    W: GPG error: https://packagecloud.io/headmelted/codebuilds/debian stretch InRelease: The following signatures couldn't be verified because the public key is not available: NO_PUBKEY 0CC3FD642696BFC8

この場合、GPG key の導入で対処できます。

    $ sudo apt-key adv --keyserver keyserver.ubuntu.com --recv-keys 0CC3FD642696BFC8

## ビルド環境の導入

ソースコードをビルドするためのツールやライブラリを導入します。

    $ sudo apt-get install build-essential zlib1g-dev libffi-dev libbz2-dev libreadline-dev libsqlite3-dev libssl-dev tk-dev

## ターミナルの日本語化

次のコマンドを実行後にターミナルを再起動します。

    $ sudo dpkg-reconfigure tzdata
    $ sudo apt -y install task-japanese locales-all fonts-ipafont
    $ sudo localectl set-locale LANG=ja_JP.UTF-8 LANGUAGE="ja_JP:ja"
    $ echo "source /etc/default/locale" >> ~/.bash_profile

## 日本語入力の追加と設定

ここでは、Googleが開発したmozcを導入します。

    $ sudo apt install fcitx-mozc

## 日本語入力の自動起動の設定

次のファイルを管理者モードで開きます。

    $ sudo vim /etc/systemd/user/cros-garcon.service.d/cros-garcon-override.conf

次の3行を最下行に追加して保存します。

    Environment="GTK_IM_MODULE=fcitx"
    Environment="QT_IM_MODULE=fcitx"
    Environment="XMODIFIERS=@im=fcitx"

次のコマンドを実行してターミナルを再起動します。

    $ echo "/usr/bin/fcitx-autostart" >> ~/.sommelierrc

## 日本語入力の切替キーの設定

日本語入力の設定ツールを起動します。

    $ fcitx-configtool

表示された画面で次の操作を実行します。

    1. [入力メソッド]タブを選択して左下の[+]ボタンをクリックする
    2. [現在の言語のみ表示]のチェックをオフにする
    3. [入力メソッドの検索]で「Mozc」と入力する
    4. 表示された[Mozc]を選択して[OK]ボタンをクリックする

次にフォントを設定します。

    1. 画面上部の「外観」タブを選択する
    2. 「フォント」をクリックする
    3. 「Search font name」をクリックして「IPA Pゴシック Regular」と入力する
    4. 表示された「IPA Pゴシック Regular」を選択して「選択」ボタンをクリックする
    5. 「メニューのフォント」も同様同に設定する
    6. 最後に[x]ボタンをクリックしてダイアログを閉じる

## VSCode(VisualStudio Code) の導入

パッケージのダウンロード

    入手先→ https://github.com/headmelted/codebuilds/releases
    ファイル名→ code-oss_1.45.0-1586135971_arm64.deb

パッケージのインストール

    ダウンロードしたファイルを右クリック（ダブルタップ）する
    [Linux（ベータ版）でのインストール]をクリックする

VSCodeの起動

    ランチャーに追加されたアイコン[Code - OSS(headmelted)]をクリックする
    ターミナルで「code-oss .」と入力するとカレントディレクトリから起動する

メニューの日本語化

    1. [ctrl]+[shift]+[x]で拡張機能を表示する
    2. 「Japanese」を検索する
    3. 「Japanese Language Pack for VS Code」をインストールする

キーバインドの変更

    ※日本語入力の切替と重なるため「候補をトリガー」のキーバインドを変更する
    1. [ctrl]+[k]+[s]でキーボードショートカットを表示する
    2. 「候補」を検索する
    3. [alt]+[space]に変更する