# Raspberry Piで学ぶROSロボット入門（C++）

上田隆一

## セットアップ

2章の内容を順に確認していきます。

### 2.1.1〜2.1.3

2.1節の内容がそのまま利用できます。ただし、ラズパイのバージョンによっては自力でのインストールは本の内容ではできなくなっているので、私や有志が用意したOSのイメージを利用します。

* OSのイメージ
	* Raspberry Pi 3B+: https://b.ueda.tech/?post=20180827_raspimouse
	* Raspberry Pi 3: https://b.ueda.tech/?post=20180617_raspi_ubuntu

イメージからのOSのインストールについては、2.1.3項の「イメージファイルの準備と書き込み」、
C.6章の「イメージからインストールしたLinuxのパーティション拡張」
を参考にして行います。

### 2.1.4〜2.5

2.1.6項「設定ファイルの修正」は、上記URLにあるイメージを使用した場合は不要です。

-----

## Linuxの性質と基本操作

3章の内容を一つずつ確認していきます。

### 補足資料

* https://github.com/ryuichiueda/robosys2018/blob/master/03.md

## GitやGitHubを用いたソフトウェアの管理

4章の内容を一つずつ確認していきます。

## パッケージのダウンロードと動作確認

5〜8章に相当する内容

### パッケージのダウンロードとビルド

`raspimouse_ros_2`をダウンロードして`catkin_make`します。

```
$ cd ~/catkin_ws/src/
$ git clone git@github.com:ryuichiueda/raspimouse_ros_2.git
$ cd ~/catkin_ws/
$ catkin_make
```

### 動作確認

## ROSでロボットを走らせる

9章の制作物をPythonではなくC++を用いて作ります。



### 9.1

パッケージを作るときにrospyとroscppを追加します。

```
$ cd ~/catkin_ws/src/
$ catkin_create_pkg pimouse_run_corridor rospy roscpp
```


