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

-----

## GitやGitHubを用いたソフトウェアの管理

4章の内容を一つずつ確認していきます。

-----

## パッケージのダウンロードと動作確認

5章に相当する内容です。5.2節で、
`raspimouse_ros`ではなくて
`raspimouse_ros_2`をダウンロードして`catkin_make`します。

```
$ cd ~/catkin_ws/src/
$ git clone git@github.com:ryuichiueda/raspimouse_ros_2.git
$ cd ~/catkin_ws/
$ catkin_make
```

あとはテキストの内容を行います。

-----

## ROSでロボットを走らせる

9章の制作物をPythonではなくC++を用いて作ります。

### 9.1

パッケージを作るときにrospyとroscppを追加します。
パッケージ名は`cpimouse_run_corridor`とでもしておきましょう。

```
$ cd ~/catkin_ws/src/
$ catkin_create_pkg cpimouse_run_corridor rospy roscpp
```

`wall_stop.launch`は、本の内容から次のように変えます。
（`wall_stop.py`ではなく`wall_stop`を実行するようにする。）

* https://github.com/ryuichiueda/cpimouse_run_corridor/blob/master/launch/wall_stop.launch


### 9.2

#### プログラミング

C++のコードの例は次のURLにあります。コードは`src`という
ディレクトリをパッケージのトップディレクトリに作り、
その下に拡張子`.cpp`で置きます。

* https://github.com/ryuichiueda/cpimouse_run_corridor/blob/master/src/wall_stop.cpp

コーディングのポイントを`main`から追っていきましょう。

* 44行目: ノードの初期化
* 45行目: ノードのハンドラを作成
* 名前空間を`~`、つまり44行目のノードの名前で指定（特に指定しなくてもよい）
* 47行目: パブリッシャの作成
* 48行目: サブスクライバの作成
    * コールバック関数`callback`を指定
    * `callback`は18〜21行目で実装
* 50, 51行目: サービスの起動までウエイト
* 53行目: Ctrl+Cを押したときに実行する関数`onSigint`（23〜28行目）の指定
    * `onSigint`ではモータの電源を落としてノードを終了
* 55, 56行目: サービスの使用
* 60, 67行目: タイマー（指定の時間に合わせてwhileが繰り返す）
    * run関数で`/cmd_vel`にパブリッシュ
* 64行目: Ctrl+Cの受け付けのために`ok`関数を利用
* 66行目: コールバック関数の呼び出し等、バックグラウンドの処理を実行

（63行目は余計かもしれません。）

#### ビルド

