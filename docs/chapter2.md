第2章 プロセス
==============

2.1 プロセスの概要
------------------
### プロセスとは
カーネルが実行中のプログラムを管理する単位
* 実行プログラムが読み込まれたメモリ領域が割り当てられる
* 仮想アドレス空間を持つ  
  MMU(Memory Management Unit)が実際のアドレス（物理アドレス）との変換を行う
* 一意のプロセスIDを持つ
* 各プロセスの仮想アドレス空間は独立  
  ※同じプログラムの命令列は共有される（テキストセグメント）

### プロセスの並行処理
TSS(タイムシェアリングシステム)
* マルチユーザ・マルチタスク
* 処理を行うプロセスをカーネルが随時切り替えることで、**CPUが1つなのに複数プログラムを並行して処理できる**

### プロセスの実行状態
* **実行可能状態**
* **休眠状態**（資源の解放や周辺デバイスの処理完了待ち）

### ユーザモードとカーネルモード
* プロセスは2つのモードを行ったり来たりする
    * ユーザモード -> カーネルモード ： **システムコール**
    * カーネルモード -> ユーザモード ： カーネル処理終了
* モードに応じて、仮想アドレスにマッピングされるメモリ領域が切り替わる
* ユーザプログラムにカーネルの機能を自由に使用させないことで、システムの安全性を高めている

### スワッピング
* メモリが足りなくなったら、休眠状態や重要度の低いプロセスをスワップ領域に退避（スワップアウト）
* 実行可能状態になったらスワップ領域から復帰（スワップイン）


2.2 proc構造体とuser構造体
--------------------------
### proc構造体
[proc.h](http://minnie.tuhs.org/cgi-bin/utree.pl?file=V6/usr/sys/proc.h)
* プロセスに関する情報のうち、**常にカーネルから必要とされる情報**
* メモリに常駐し、スワップアウトされない
* 同時に存在できるプロセスの最大数は```NPROC = 50``` ([param.h](http://minnie.tuhs.org/cgi-bin/utree.pl?file=V6/usr/sys/param.h))

### user構造体
[user.h](http://minnie.tuhs.org/cgi-bin/utree.pl?file=V6/usr/sys/user.h)
* プロセスがオープンしたファイルやカレントディレクトリの情報など
* 対応するプロセスと一緒にスワップアウトされる
* グローバル変数```u=0140000```は**実行プロセスのuser構造体を指す**

2.3 プロセスに割り当てられるメモリ
----------------------------------
### テキストセグメント
* プログラムの命令列を格納する領域  
* **読み取り専用**  
* 同じプログラムを実行するプロセス間で**共有される**  
* text構造体で管理される（[text.h](http://minnie.tuhs.org/cgi-bin/utree.pl?file=V6/usr/sys/text.h) 詳細は4章）

### データセグメント
* プログラムが使用する変数などのデータを格納する領域
* **共有されない**

#### PPDA(Per Process Data Area)
user構造体とカーネルスタック領域（カーネル処理の作業領域）  
サイズ固定（USIZE × 64バイト ＝ 1Kバイト）  
ユーザ空間からアクセスできない（ユーザプロセスの仮想アドレス空間に割り当てられない）

#### データ領域
静的変数（グローバル変数、[bss](http://ja.wikipedia.org/wiki/.bss)）とヒープ領域（mallocとか）  
拡張するにはシステムコールが必要

#### スタック領域
関数の引数やローカルデータ  
必要に応じて自動的に拡張される


### 仮想アドレス空間
各プロセスには64KB（16ビットアドレス）の仮想アドレス空間が与えられる
* テキストセグメント：最下位アドレス
* データ領域：その後ろ（8Kバイト刻み）
* スタック領域：最上位アドレス

仮想アドレスを用いるメリット
* プログラムは任意のアドレスから始まるアドレスを使用できる
* アクセス管理を行える
* メモリの使用効率を高める

### アドレス変換
APR (Active Page Register) 
* PAR (Page Address Register)
* PDR (Page Description Register)



