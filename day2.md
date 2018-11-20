# Raspberry Piで学ぶROSロボット入門（C++）

上田隆一

## シェルスクリプトの記述とGitHubへのアップロード

* 3.3節から4章までを忠実に。
* その後、cpimouse_run_corridorをGitHubにアップしましょう。

## ローカルでのパッケージのテスト

基本的に入出力だけ確認できれば良いので、とりあえず以下の手順で。

* デバイスドライバの代わりに`/dev/`下にダミーファイルを置く
    * https://github.com/ryuichiueda/cpimouse_run_corridor/blob/master/test/travis_prepare_dummy_files.bash を実行

* 手でダミーのデバイスファイルの値を確認

``` 
$ roslaunch cpimouse_run_corridor wall_stop.launch
###モータへの出力の初期値を確認###
$ head /dev/rtmotor_raw_?0 
==> /dev/rtmotor_raw_l0 <==
566

==> /dev/rtmotor_raw_r0 <==
566
###ダミーのセンサ値を入力###
$ echo 1000 1000 1000 1000 > /dev/rtlightsensor0 
###モータへの出力が変化していることを確認###
$ head /dev/rtmotor_raw_?0
==> /dev/rtmotor_raw_l0 <==
0

==> /dev/rtmotor_raw_r0 <==
0
``` 

* 自動のシェルスクリプトを書く
    * キーワード: 終了ステータス
    * https://github.com/ryuichiueda/cpimouse_run_corridor/blob/master/test/manual_test.bash

## Pythonでテストを書く

C++でもいいんですがちょっと冗長になってしまうので、こういうものはPythonでさらっと書けると捗ります。

* 例
    * https://github.com/ryuichiueda/cpimouse_run_corridor/blob/master/test/travis_test_wall_stop.py
* ポイント
    * `self`: 自分への参照
    * `assert...`: 引数の内容をチェックして、ダメだとコードを止めて非ゼロの終了ステータスを返す

## Travis CIでのテスト

### 準備

* Sign In with GitHub
* 顔アイコン->Settingsで`cpimouse_run_corridor`のスイッチON

