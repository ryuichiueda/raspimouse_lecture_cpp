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

* SSHで入ったら以下を確認
	* エディタ、vimtutor
	* コンパイル言語のコーディングと実行
	* スクリプト言語のコーディングと実行

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
$ rosdep install raspimouse_ros_2
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

#### CMakeLists.txtの編集

ビルドについては書籍で使用しているPythonとは異なり、
ヘッダファイルのインクルードやライブラリとのリンクの
設定を記述しなければならないので、`CMakeLists.txt`
を適切に設定する必要があります。

書籍（`catkin_create_pkg`が出力するもの）と
C++用の`CMakeLists.txt`の違いは次の通りです。

* add_executable: 実行するノードの実行ファイルと、元になるcppファイルを記述
    * `add_executable(wall_stop src/wall_stop.cpp)`
* add_dependencies: 実行するノードの実行ファイルが依存するものが先にビルドされるように設定
    * https://cmake.org/cmake/help/latest/command/add_dependencies.html
    * `add_dependencies(wall_stop ${${PROJECT_NAME}_EXPORTED_TARGETS} ${catkin_EXPORTED_TARGETS})`
* target_link_libraries: リンクするライブラリの指定
    * https://cmake.org/cmake/help/latest/command/target_link_libraries.html?highlight=target_link_libraries#libraries-for-a-target-and-or-its-dependents
    * `target_link_libraries(wall_stop ${catkin_LIBRARIES})`

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

#### C++ その1

`wall_stop.cpp`内の関数の一部をクラスにしてみましょう。

* https://github.com/ryuichiueda/cpimouse_run_corridor/blob/class/src/wall_stop.cpp

#### C++ その2

クラスの内容を`.h`ファイル、`.cpp`ファイルに分離してみましょう。

* https://github.com/ryuichiueda/cpimouse_run_corridor/blob/class2/src/

* [CMakeLists.txt](https://github.com/ryuichiueda/cpimouse_run_corridor/blob/class2/CMakeLists.txt)の編集
    * C++14（11, 17, 20）を使えるようにする: `add_compile_options(-std=c++14)`
    * ライブラリの追加: `add_library(WallStop src/WallStop.cpp)`
    * `add_dependencies`の追加: `add_dependencies(WallStop ${${PROJECT_NAME}_EXPORTED_TARGETS} ${catkin_EXPORTED_TARGETS})`
        * 他のパッケージのライブラリを使うときに必要（この場合は`raspimouse_ros_2`）
    * `target_link_libraries`の編集: `target_link_libraries(wall_stop WallStop ${catkin_LIBRARIES})`
        * `WallStop`を追加

#### その他のコード

余裕のある場合は`wall_stop_accel.cpp`、`wall_trace.cpp`、`wall_around.cpp`をやってみる。
