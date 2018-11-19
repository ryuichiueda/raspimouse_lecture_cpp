# Raspberry Piで学ぶROSロボット入門（C++）

上田隆一

## シェルスクリプトの記述とGitHubへのアップロード

3.3節から4章までを忠実に。

その後、cpimouse_run_corridorをアップしましょう。

### ローカルでテスト

基本的に入出力だけ確認できれば良いので、とりあえず以下の手順で。

* デバイスドライバの代わりに`/dev/`下にダミーファイルを置く
    * https://github.com/ryuichiueda/cpimouse_run_corridor/blob/master/test/travis_prepare_dummy_files.bash を実行

* 手でダミーのデバイスファイルの値を確認

``` 
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

* センサの値をダミーファイルにセット

* モータの出力を確認

## Travis CIでのテスト

### 準備

* Sign In with GitHub
* 顔アイコン->Settingsで`cpimouse_run_corridor`のスイッチON

