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

### 実行

launchファイルにテストのための要素を書きます。

```
$ cat ../launch/wall_stop.launch
<launch>
  <include file="$(find raspimouse_ros_2)/launch/raspimouse.launch" />
  <node pkg="cpimouse_run_corridor" name="wall_stop" type="wall_stop" required="true" />
  <test test-name="test_wall_stop" pkg="cpimouse_run_corridor" type="travis_test_wall_stop.py" />
</launch>
```

実行します。

```
$ ./travis_prepare_dummy_files.bash
$ rostest cpimouse_run_corridor wall_stop.launch
... logging to /home/ueda/.ros/log/rostest-ubuntu-8352.log
[ROSUNIT] Outputting test results to /home/ueda/.ros/test_results/cpimouse_run_corridor/rostest-launch_wall_stop.xml
[Testcase: testtest_wall_stop] ... ok

[ROSTEST]-----------------------------------------------------------------------

[cpimouse_run_corridor.rosunit-test_wall_stop/test_io][passed]

SUMMARY
 * RESULT: SUCCESS
 * TESTS: 1
 * ERRORS: 0
 * FAILURES: 0

rostest log file is in /home/ueda/.ros/log/rostest-ubuntu-8352.log
```

## Travis CIでのテスト




### 準備

* Sign In with GitHub
* 顔アイコン->Settingsで`cpimouse_run_corridor`のスイッチON

