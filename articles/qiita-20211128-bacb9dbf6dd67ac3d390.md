---
title: "Factory IO のPLC(FX5U)プログラム例(Assembler編)"
emoji: "😀"
type: "tech"
topics: [PLC,MELSEC,Factory_IO]
published: false
---
前回は通信設定をしたので今度は作成したプログラムの動作説明です。

https://qiita.com/fa_yoshinobu/items/477cba75747993b01725

Assemblerという設備のプログラム例です。
無限に表れる二つの部品を合体させる機械です。
作成したプログラムの動作は動画で字幕で説明しています。
字幕だと翻訳も出来るし手間が最小限なので便利ですね。
<iframe width="560" height="315" src="https://www.youtube.com/embed/eumEbKI5u6s" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

# Factory IOの良いところ
ラダーというプログラムを勉強するにはどこかの会社でOJTで習うのが一般的です。
仕事で作成したプログラムや設備の仕様をネットに公開するのは機密保持があるのでまずいです。
そういう事情があるので、ネットでラダーの説明をしてるブログもありますが具体例は書きにくいのです。
Factory IOを使うことで架空の案件を作れるので、プログラムの作成例を公開しやすいと思います。

# ファイル置き場
GX Works3のプロジェクトはGithubにおいてます。
HTML変換したラダーも入れたので、GX Works3がなくても見ることはできます。

https://github.com/fa-yoshinobu/fa-yoshinobu.github.io/tree/main/Factory%20IO%20examples/MELSEC%20iQ-F%20FX5U/Scenes/Assembler

# ラダーHTML表示
## BASE側制御
https://fa-yoshinobu.github.io/Factory%20IO%20examples/MELSEC%20iQ-F%20FX5U/Scenes/Assembler/GXW3_Export_Ladder/B_CV.html
## LID側制御
https://fa-yoshinobu.github.io/Factory%20IO%20examples/MELSEC%20iQ-F%20FX5U/Scenes/Assembler/GXW3_Export_Ladder/L_CV.html
## 運転制御
https://fa-yoshinobu.github.io/Factory%20IO%20examples/MELSEC%20iQ-F%20FX5U/Scenes/Assembler/GXW3_Export_Ladder/CTRL.html

# 説明
## 工程歩進制御
こういう設備だとコンベアやアクチュエータを決められた順番で動かすだけで実現可能です。
工程歩進制御とか言ったりします。

DECO命令は数値をビットに変換します。

M200～のデバイスはR0の数値によりどこかがONになります。
例1.K0のときはM200だけがONします。
例2.K1のときはM201だけがONします。
例3.K8のときはM208だけがONします。

![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/2146151/18dfffff-c1e7-d4cf-e1d6-a9316abfa606.png)

初期状態はM200だけがONになります。
スタート(g_b_EQ_Start)するとR0にK1がMOVされて1スキャンしたあとにM201だけがONします。
M201がONすると、R0にK2がMOVされて1スキャンしたあとにM202だけがONします。
動作はこの繰り返しですね。
![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/2146151/5dd99cf1-baa6-43e3-8150-b494078c74d6.png)

そして工程を進めずに待ちたい場合はMOVの前に次の工程に進めるための条件を入れます。
下記の例ではワークが流れて通過し終わるとコンベアを止めます
通過中はmb_DI03がON,完全に通り過ぎるとmb_DI03はOFFになります。
mb_DI03にはこのセンサのON/OFFが格納されます。
![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/2146151/cf42e1fa-407f-4e98-a61b-2501b18b70e8.png)
こんな感じで工程を進めたり止めたりします。

つまりM201-M203がONの間はコンベアを動かす必要があり、M204になるとコンベアを止める必要があります。
ですのでM201,M202,M203のORをコンベア出力(mb_DO03)条件にすればよいのです。
これがORの回路です。なんか見た目が違いますね。
![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/2146151/260ffaf6-ca01-8f65-6e5f-01409dd37738.png)

ORは下の図のように本来は書きます。
ORの条件が増えると縦方向にどんどん伸びていきます。
3個ぐらいなら別にいいですがORが15個ぐらいあると画面1ページでは収まらなくてしんどくなります。
ということでORを横方向に書くという手法がよく使われます。
OR変形してANDで表現すると上のような回路で実現できます。上と下の回路は同じ動作をします。
![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/2146151/c72f4b2f-d1e1-9b05-a373-7cbdce95da32.png)

コレを全部まじめにORで書くと縦方向に長くなりすぎて気が狂いそうですね。
![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/2146151/f2276ea5-5a97-e30e-8d62-ff9943c16192.png)

一番最後まで行くと先頭のM200に戻す必要があるので最後はK0をMOVします。
これでぐるぐる回すことができます。
![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/2146151/f46aac63-6f2d-bd46-bd46-5d4aeae7bf65.png)

工程歩進制御はこんな風に作ります。
海外だとSFCを使うのが一般的なのかもしれません。（しらんけど）
なのでこのような書き方はジャパニーズスタイルのような気がします。

LID側（上のパーツ）とBASE側(下のパーツ)で別々に工程を組んで受け渡しが必要なところはお互いの工程を条件に入れて待つようにしています。
![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/2146151/73b7e33a-65d8-799f-2196-2dbb741588c4.png)
![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/2146151/45acdded-b002-7d21-6b92-20208e89080f.png)

## 運転開始/停止制御

運転開始と停止は自己保持で作ります。
スタートボタン(mb_DI10)で開始、非常停止とかを押すと自己保持を切ります。
![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/2146151/34e57383-6be3-31e3-a4e2-057e16cc4af0.png)

停止ボタンで単純に設備を即停止するのでは非常停止と動作が同じになりつまらないので、少し工夫してサイクル停止するようにしました。
サイクル停止とは一連の動作が終わったタイミングで設備を止めることです。

この設備は単純なのでいいですが産業用ロボットとかを使うと動作中に止めると変な場所で止まって困ることになります。
なのでロボットがホームポジションに戻ったときに動作を停止させたりします。
変なタイミングで止めると再スタートのときにうまく動かないことがあるのでこのようなプログラムにしています。

この設備では上のパーツと下のパーツを合体させたときに停止するようにしています。
M200とM312がONするときがそのタイミングです。
![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/2146151/4d4d24b2-bc98-c2b4-37cb-84f214706fc3.png)

サイクル停止を実行するとg_b_Cycle_StopがONになります。
実際にはM200とM312がうまく同時にONするタイミングは来ないので、一連の動作が終わったときにお互いの工程を進めないようにB接点を入れて工程を待たせるように細工をします。
![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/2146151/adcdfd93-0c41-5f58-4140-7f6208f83686.png)
![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/2146151/4eaecbd6-ae8f-583d-ab81-c1fcfba29519.png)

工程を待たせてM200とM312が両方ONになるとb_Cycle_Stop_PosがONになります。
この条件でg_b_EQ_Startの自己保持を切ります。
おおなんか良い感じですね。
![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/2146151/a3f9f791-d8fc-410d-8219-7c78f070b0e4.png)
![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/2146151/e9326870-421e-2126-7cb5-d2662beb67af.png)

# おわり
いかがでしたか？
全部細かく書くと大変なので端折っていますので詳細はプログラムの方を見てください。
もっと俺の方がエレガントなプログラムを書けるぜみたいな方がいらっしゃいましたら是非記事を書いてみてください。
他にもいろいろな方法があると思うので。

# 補足：ステートマシン
工程歩進制御と呼ばれるものですが正しい名前はステートマシンというのが正しいかもしれません。

https://abedgnu.github.io/Automation-Notes/chapters/PLC/Siemens/fsm.html

この動画はArduinoの場合の説明です。
結局は数字を変えることで状態遷移させてるのでやってることは一緒なのかな
<iframe width="560" height="315" src="https://www.youtube.com/embed/v8KXa5uRavg" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

