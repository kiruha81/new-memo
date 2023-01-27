#### GitHubでコミットした際の時間が世界標準時間(UTC)になっていたため
#### タイムゾーンを日本時間(JST)へ変更
##### https://dot-blog.jp/news/cloud9-linux-changing-timezone/

# sudoコマンドで実行していく
### sudoとは
##### https://wa3.i-3-i.info/word11269.html

    sudo cp /etc/sysconfig/clock /etc/sysconfig/clock.org
##### cp = コピー

### ファイル確認 8行目くらい

    ls -la /etc/sysconfig/

-rw-r--r--  1 root root   27 Jan 27 18:21 clock

-rw-r--r--  1 root root   20 Jan 27 18:18 clock.org

# clockファイルをvimコマンドで変更する

    sudo vim /etc/sysconfig/clock
##### vim = テキストエディタ

    ZONE="UTC"
    UTC=true

↓

    ZONE="Asia/Tokyo"
    UTC=true
#### iを入力して編集モード→Escで編集終了→:wqと入力してEnterで終了

# リンク先を編集したファイルに変更

    sudo ln -sf /usr/share/zoneinfo/Asia/Tokyo /etc/localtime

# Cloud9を再起動

    sudo reboot

# 編集が反映されているか確認

    cat /etc/sysconfig/clock

ZONE="Asia/Tokyo"

UTC=true

    cat /etc/localtime

JST-9


# 時間を確認

    date

Fri Jan 27 18:41:48 JST 2023