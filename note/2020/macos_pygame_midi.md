# MIDI用の信号をMacOSで扱えるようにする

参考：https://stackoverflow.com/questions/49543366/why-pygame-midi-doesnt-work-on-macos-whereas-pygame-mixer-does
MIDI用の信号は普通のオーディオのOUTPUTでは使えないため、一度MIDI信号をDTMアプリなどを介してやるようにする必要がある。そのために、MacOSではAudio MIDI Setupという標準で入っているアプリを使用してDTMアプリに信号を渡してやるようにする。
まず、Audio MIDI Setupアプリを起動して、Window→Show MIDI Studioを選択。
次に、MIDI Studioの中のIAC Driverというものをダブルクリックして開く。
次にDevice is Onlineにチェックを入れる事で、MIDI信号をGarageBandなどのDTMアプリに自動で入力してくれるようになる。
ちなみに、GarageBandを立ち上げている状態でこのDevice is onlineにチェックを入れると、下のような通知がくる。
これで、GarageBandで新規Projectを作成したら、pythonで作成した信号を聞く事ができる。

# pythonからMIDI用の信号を作成し、音を再生する
まずはpygameをinstallする。

```
$ python3 -m pip install pygame
```
次に、下のようにpythonファイルを作成する。

```
import pygame.midi
import time

pygame.midi.init()
player = pygame.midi.Output(1)
player.set_instrument(0)
player.note_on(64, 127)
time.sleep(1)
player.note_off(64, 127)
del player
pygame.midi.quit()
```
これを実行するとGarageBand経由で音が聞こえるはず。
player.set_instruments(0)で楽器を選択しており、General_MIDI(wiki)では128（pythonのindexでは127）まであるらしい。
garagebandを使って再生するとgarageband側で設定した音でしか聞こえなかった。

# Docker上のpythonでMIDIファイルを再生する

python:latest（2020/05/26現在ではpython3.8.2）のdocker image上で再生してみる。
まず、pygameを入れる必要があるが、そのために以下のものをapt-getでインストールしないといけない。
```
apt-get install -y libsdl-dev libsdl-image1.2-dev libsdl-mixer1.2-dev libsdl-ttf2.0-dev
apt-get install -y libsmpeg-dev libportmidi-dev libavformat-dev libswscale-dev
```
これでpygameをinstallする事ができる。
```
python3 -m pip install pygame
```
次に、docker上で再生した音はそのままでは聴けないので、ホスト側に渡す必要がある。
調べてみると
