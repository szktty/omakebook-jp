.. _FirstStep:

================
3 章: 最初の一歩
================

この章では、簡単なプロジェクトを通じて OMake の基本的な使い方を見ていきます。


プロジェクトの準備
==================

ビルドするファイルは C ソースファイル一つのみとします (``hello.c``) 。ソースファイルの中身はよくある Hello, world を出力するだけのコードです。

.. note:: C について詳しくなくても問題ありません。 C ソースファイルは C コンパイラ (``cc`` コマンドや ``gcc`` コマンドなど) で実行可能なプログラムにできる、ということだけ頭に入れておいてください。

``hello.c``::

 #include <stdio.h>

 int main(int argc, char **argv) {
   printf("Hello, world!\n");
   return 0;
 }

.. index:: OMakeroot, OMakefile, omake コマンド; --install

``hello.c`` ファイルを用意したら、そのディレクトリで次のコマンドを実行してください。

::

 % omake --install
 *** omake: creating OMakeroot
 *** omake: creating OMakefile
 *** omake: project files OMakefile and OMakeroot have been installed
 *** omake: you should edit these files before continuing

このコマンドを実行すると、 ``OMakeroot`` ファイルと ``OMakefile`` ファイルが生成されます。 OMake ではこれらのファイルにビルドの設定を書きます。自分で空のファイルを生成しても大丈夫ですが、 ``omake --install`` コマンドを使うとテンプレートを生成してくれるので便利です。

``omake --install`` 実行後のディレクトリ構成::

 OMakeroot
 OMakefile
 hello.c

これで準備ができました。 ``OMakeroot`` ファイルと ``OMakefile`` ファイルの違いについては、後々触れていきます。


OMake を実行してみる
====================

``OMakeroot`` ファイルと ``OMakefile`` ファイルを用意できたので、これで OMake を実行できるようになります。まだビルドの設定を何も書いていませんが、 OMake を実行してみましょう。同じディレクトリで ``omake`` コマンドを実行します。

::

 % omake
 *** omake: reading OMakefiles
 ./OMakefile is not configured
 *** omake: finished reading OMakefiles (0.19 sec)
 *** omake: done (0.19 sec, 0/0 scans, 0/0 rules, 10/47 digests)

上記のメッセージが表示されれば成功です。 ``*** omake:`` で始まる行は OMake のログメッセージです。最後に実行に要した時間を表示しています。

二行目に ``./OMakefile is not configured`` (``OMakefile`` は設定されていない) というメッセージが表示されています。これは ``--install`` オプションで生成した ``OMakefile`` ファイルに含まれている処理です。 ``OMakefile`` ファイルの内容はこのメッセージの表示と書き方についてのコメント ( ``#`` で始まる行) のみですので、すべて削除しても構いません。  ``OMakefile`` ファイルを空にしてから ``omake`` コマンドを実行すると、先程表示されていたメッセージが表示されなくなります。

::

 % omake
 *** omake: reading OMakefiles
 *** omake: finished reading OMakefiles (0.01 sec)
 *** omake: done (0.01 sec, 0/0 scans, 0/0 rules, 1/41 digests)

``OMakeroot`` ファイルはそのままにしておいてください。まだ編集する必要はありません。


.. index:: キャッシュファイル, OMakeroot.omc, OMakefile.omc, .omakedb, .omakedb.lock
.. note:: **OMake のキャッシュファイル**

   OMake を実行すると、 ``OMakeroot.omc`` ファイルと ``OMakefile.omc`` ファイル が生成されます。拡張子が ``omc`` のこれらのファイルは、 OMake が使うキャッシュファイルです。 OMake はこれ以外にも自身が使う ``.omakedb`` ファイルと ``.omakedb.lock`` ファイルを生成します。 OMake のコードを変更したのに反映されていないと思ったら、これらのキャッシュファイルを削除してから実行するとうまくいくことがあります。


``hello.c`` ファイルのビルド
============================

それでは ``hello.c`` ファイルのビルドの設定を行います。 ``OMakefile`` ファイルに次のコードを記述してください。 2 行目のインデントはタブでもスペースでも構いません。

::

 hello: hello.c
     cc -o hello hello.c

``omake`` コマンドの引数に ``hello`` を渡して実行すると、 ``hello.c`` ファイルがコンパイルされて ``hello`` コマンドが生成されます。

::

 % omake hello
 *** omake: reading OMakefiles
 *** omake: finished reading OMakefiles (0.01 sec)
 *** omake: done (0.44 sec, 0/0 scans, 1/1 rules, 2/73 digests)

 % ls
 OMakefile       OMakeroot       hello
 OMakefile.omc   OMakeroot.omc   hello.c

 % ./hello
 Hello, world!

何らかのエラーが表示されたら、 ``OMakefile`` ファイルのコードが正しいかどうか、コンパイルのコマンド (``cc -o hello hello.c``) が正しく実行できるかどうか確認してください。例えば次のエラーは、 ``cc`` コマンド (C コンパイラ) が見つからないと表示されます。もしこのエラーが表示されたら、 C コンパイラのインストールの有無やコマンドパスを確認してください。

例) ``cc`` コマンドが見つからない場合のエラー::

 % omake hello
 *** omake: reading OMakefiles
 *** omake: finished reading OMakefiles (0.01 sec)
 - build . hello
 + cc -o hello hello.c
    *** process creation failed:
    *** omake error:
       File OMakefile: line 2, characters 4-24
       command not found in PATH: cc
 *** omake: 12/13 targets are up to date
 *** omake: failed (0.02 sec, 0/0 scans, 1/1 rules, 11/72 digests)
 *** omake: targets were not rebuilt because of errors:
    hello
       depends on: hello.c


.. index:: ルール, ターゲット

ルールとターゲット
==================

今 ``OMakefile`` ファイルに記述したコードは、 ``hello.c`` ファイルをビルドするための **ルール** です。このコードの目的は、「 ``hello.c`` ファイルをコンパイルして、実行可能なプログラム ``hello`` を生成する」ことでした。 OMake はこれを「 ``hello`` プログラムを生成するために必要なものは ``hello.c`` ファイルで、生成には ``hello.c`` ファイルをコンパイルするコマンドを実行する」と、結果から必要な処理をたどる解釈をします。この生成されるべき結果を **ターゲット** と呼びます。

``hello`` プログラムを生成するルール (OMake の解釈)::

 # 結果として生成されるファイル: 必要なファイル
 hello: hello.c
     # 結果を出すための処理
     cc -o hello hello.c

このビルドを実行したコマンド ``omake hello`` は、「 ターゲット ``hello`` (``hello`` ファイル) をビルドせよ」という意味です。 OMake は ``hello`` ファイルをビルドするためのルールに従って ``hello.c`` ファイルをコンパイルします。その結果 ``hello`` ファイルが生成されれば、ルールとビルドは成功です。

このように、ルールとは「ターゲットを生成するために必要な (= 依存する) ターゲットとコマンド」です。あらためて基本的な文法を次に示します。

ルールの文法::

 ターゲット ... : 依存するターゲット ...
   コマンド
   ...

.. index:: インデント

ターゲットには、ビルドで生成されるファイル名を指定します。依存するターゲットには、ターゲットのビルドを行う前に生成されていなければならないファイル名を指定します。コマンドには、ターゲットをビルドするコマンドをインデントして記述します。インデントはタブでもスペースでも構いませんが、混在させてはいけません。また、同じブロックでインデントの深さを変えてはいけません。

インデントの間違いの例::

 # タブとスペースを混ぜる (見えませんが)
 hello: hello.c
   cc -o hello.o hello.c # タブ
   cc -o hello hello.o   # スペース

 # 同じブロックでインデントの深さが異なる
 hello: hello.c
   cc -o hello.o hello.c
     cc -o hello hello.o

 # 制御構文など、ブロックが深くなる場合はインデントの深さは変わる
 hello:
   if true
     echo Hello, world!

   # ただし、前のブロックとインデントの深さを揃えなくてもいいらしい
   if true
           echo Hello, world, again!


``OMakeroot`` ファイルと ``OMakefile`` ファイル
===============================================

ここまでで、 ``hello.c`` ファイルのビルドについて一通り見てきました。 ``hello.c`` ファイルのビルドルールにはまだ改良の余地がありますが、その前に ``OMakeroot`` ファイルと ``OMakefile`` ファイルについて説明します。

``OMakeroot`` ファイルと ``OMakefile`` ファイルの内容に違いはありません。どちらにどのようなビルドの設定を書こうが自由です。二つのファイルの違いは、 ``OMakeroot`` ファイルはプロジェクトのルートディレクトリにのみ置けるのに対し、 ``OMakefile`` ファイルは各サブディレクトリにも置けることです。サブディレクトリを含むプロジェクトの多くは、次のようにビルドファイルを配置します。サブディレクトリを含むプロジェクトについては、 :ref:`プロジェクト管理 <ProjectManagement>` で詳しく扱います。

``OMakeroot`` ファイルと ``OMakefile`` ファイルの配置::

  OMakeroot
  OMakefile
  doc/
    OMakefile
    ...
  src/
    OMakefile
    ...

``OMakeroot`` ファイルは必ず用意しなければなりません。OMake は ``OMakeroot`` ファイルのあるディレクトリを、プロジェクトのルートディレクトリとして認識します。サブディレクトリで OMake を実行すると、 OMake は ``OMakeroot`` ファイルを探してルートディレクトリを確認します。

ルートディレクトリを示すこと以外に ``OMakeroot`` ファイルと (ルートディレクトリにある)  ``OMakefile`` ファイルの違いはありませんが、慣習的には次のように使い分けられているようです。

*  プロジェクト全体から参照される、または影響する設定はルートディレクトリの ``OMakefile`` に書き、 ``OMakeroot`` には基本的に手を加えないようにします。 OMake のドキュメントではこちらを推奨しています。
* グローバルな設定を ``OMakeroot`` ファイルに書き、ルートディレクトリの ``OMakefile`` ファイルには、ルートディレクトリで行うべき設定を書きます。

.. index:: make との違い; サブディレクトリの扱い

.. note::  **make との違い**

   make でも ``Makefile`` ファイルを各サブディレクトリに置いてプロジェクトを管理することがよくあります。ただし、 make では各サブディレクトリに置いた ``Makefile`` ファイルは、あくまで個別に実行される ``Makefile`` ファイルです。親ディレクトリの ``Makefile`` ファイルからサブディレクトリのビルドを行う場合は、サブディレクトリに移動して make を実行します。そのため親ディレクトリで動く make のプロセスとサブディレクトリで動く make のプロセスは別になり、依存関係の設定がサブディレクトリで断たれてしまいます。

   OMake では、サブディレクトリの ``OMakefile`` ファイルも含めてプロジェクト全体の依存関係を構築します。サブディレクトリを管理するために、ルートディレクトリを示す ``OMakeroot`` ファイルが必要となります。



もう少し便利にする
==================

.. index:: -P, 継続監視ビルド

ファイルを変更したら自動的に再ビルドする
----------------------------------------

ここまでは、ファイルを変更するたびに ``omake`` コマンドを実行してビルドを行ってきました。 OMake では ``-P`` コマンドラインオプションを指定すると、ファイルを監視して自動的にビルドを再実行してくれます (以降、 **継続監視ビルド** と呼びます) 。いちいちコマンドを入力して、ビルドを待って、結果を確認して...というお決まりの手間とストレスをずいぶんと減らせます。ぜひ `"OMake 継続監視ビルド" で検索 <http://www.google.co.jp/search?hl=ja&source=hp&q=OMake+%E7%B6%99%E7%B6%9A%E7%9B%A3%E8%A6%96%E3%83%93%E3%83%AB%E3%83%89&aq=f&aqi=&aql=&oq=&gs_rfai=>`_ して、その効果を実感された方々の声をご覧下さい。まあ自分で試してみるのが一番ですが。

``-P`` オプションを指定して ``omake`` コマンドを実行すると、次のように表示されてファイルの監視が始まります。

::

 % omake -P hello
 *** omake: reading OMakefiles
 *** omake: finished reading OMakefiles (0.01 sec)
 *** omake: done (0.05 sec, 0/0 scans, 1/1 rules, 2/66 digests)
 *** omake: polling for filesystem changes

この状態で ``hello.c`` ファイルの内容を適当に変更してみてください。

``hello.c`` を変更してみる::

 #include <stdio.h>

 int main(int argc, char **argv) {
   /* メッセージを適当に変更 */
   printf("ciao, monde!\n");
   return 0;
 }

すると、 ``omake -P`` を実行中のコンソールに反応があるはずです。次のようなメッセージが表示され、再びファイルの監視が始まります。 ``rebuilding`` と表示されれば、ビルドは再実行されています。 ``hello`` コマンドを実行してみて、変更が反映されているか確認してください。

::

 *** omake: file hello.c changed
 *** omake: rebuilding
 *** omake: done (0.04 sec, 0/0 scans, 2/2 rules, 6/91 digests)
 *** omake: polling for filesystem changes

さらに詳しいビルドの実行方法は、 :ref:`6 章: ビルドの実行 <ExecutingBuild>` を参照してください。なお、環境によっては継続監視ビルドがうまく動かない場合もあるようです。原因は不明ですが、そうなった場合はインストール方法を見直すといいかもしれません。 :ref:`15 章: トラブルシューティング <PollingTrouble>` も参照してください。

.. note:: **ある対話**

   |
   | **N**: Java で実装されたコマンドは本当に遅いよねえ。どうにかしてよドエライモン。
   | **S**: どうにかしたいと考えている人もやはりいるようで、検索するとちらほらツールが出てきますね。僕もサーバを作って Java を常駐させてクライアントコマンドは keritai で Scala でビルドツールを作れば...とか考えたことがありますが面倒くさくなって止めました。
   | **N**: なんですか keritai って。
   | **S**: 蹴りたい Java 。
 

.. index:: .DEFAULT
   single: ターゲット; ターゲット名の省略

ターゲットの指定を省略する
--------------------------

毎回 ``omake hello`` のように同じターゲット名を指定するのは面倒です。また、他の人がこのプログラムをビルドするにも、 ``omake`` の入力だけで済むなら少しでも楽をしてもらえます。

ターゲット名を省略するには、 ``.DEFAULT`` という特殊なルールを定義します。このルールにターゲット (を生成するルール) を指定すると、コマンドラインでターゲット名が省略されたときに実行されます。 ``hello`` ターゲットを生成して欲しい場合は、次のようにします。

``.DEFAULT`` に ``hello`` ターゲットを指定する (``OMakefile``)::

 hello: hello.c
     cc -o hello hello.c

 .DEFAULT: hello

``.DEFAULT`` を記述する位置に注意してください。 ``.DEFAULT`` に指定するルールは、 ``.DEFAULT`` よりも前に定義済みでなければいけません。

.. note:: **make all を OMake で**

   make ではターゲット名を省略すると、 ``all`` ターゲットが実行されます。 make と同じようにしたければ、 ``.DEFAULT`` に ``all`` を指定します。わざわざ言われなくてもすぐわかると思いますが、一応。


C プログラムをビルドする関数を使う
----------------------------------

OMake では関数も使えます (定義もできます) 。ビルドに便利な関数も多く用意されており、ルールを定義する代わりに関数にファイルリストを渡すだけで済むこともあります。 C プログラムをビルドする関数 ``CProgram`` を使って ``hello.c`` をビルドしてみましょう。 ``hello`` ルールを消して、次のように ``.DEFAULT`` ルールを定義します。

.. index:: CProgram()

``OMakefile``::

 .DEFAULT: $(CProgram hello, hello)


実行結果::

 % omake
 *** omake: reading OMakefiles
 *** omake: finished reading OMakefiles (0.05 sec)
 --- Checking for gcc... (found /usr/bin/gcc)
 --- Checking for g++... (found /usr/bin/g++)
 *** omake: done (0.12 sec, 1/1 scans, 2/2 rules, 13/84 digests)

 % ls
 OMakefile       OMakeroot       hello           hello.o
 OMakefile.omc   OMakeroot.omc   hello.c

``CProgram`` 関数は二つの引数を取ります。一つは生成するプログラム名、もう一つはソースファイル名のリストです。ここではどちらも ``hello`` です。

上のコードを見て、次の疑問が浮かぶと思います。

* ``$(...)`` の記法は？
* リストっぽい記述がない？
* ``Checking for gcc...`` は gcc のパスを調べてるのか？
* なぜ ``CProgram`` 関数呼び出しを ``.DEFAULT`` ルールとして指定できるのか？
* ``CProgram`` 関数は何やってんの？
* 関数やルールの実行順序はどうなってる？

関数は確かに便利ですが、このへんから OMake が何をやっているのかわかりにくくなってきます。見た目が make に似ているだけになおさらです。今は「関数を使うと楽ができる」とだけ覚えておけば十分です。


処理の流れ
==========

* OMakeroot の探索
* OMakefile のロード。上から順に評価
* 依存関係グラフの生成、静的ルールの定義
* ターゲットルールの実行

依存関係グラフは動的に更新される


``hello.c`` の例


おまけ: 予習
============

インストールされる ``OMakeroot`` ファイルの内容
-----------------------------------------------

インストールされる (``--install`` オプションで生成される) ``OMakefile`` ファイルの内容はほとんどがコメントアウトされていますが、 ``OMakeroot`` ファイルにはいくらかコードが含まれています。次にインストールされる ``OMakeroot`` ファイルの内容を示します。ここではライセンスの記述以外を訳してあります。

インストールされる ``OMakeroot`` ファイル::

 ########################################################################
 # Permission is hereby granted, free of charge, to any person
 # obtaining a copy of this file, to deal in the File without
 # restriction, including without limitation the rights to use,
 # copy, modify, merge, publish, distribute, sublicense, and/or
 # sell copies of the File, and to permit persons to whom the
 # File is furnished to do so, subject to the following condition:
 #
 # THE FILE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND,
 # EXPRESS OR IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES
 # OF MERCHANTABILITY, FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT.
 # IN NO EVENT SHALL THE AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM,
 # DAMAGES OR OTHER LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR
 # OTHERWISE, ARISING FROM, OUT OF OR IN CONNECTION WITH THE FILE OR
 # THE USE OR OTHER DEALINGS IN THE FILE.

 ########################################################################
 # 標準的な OMakeroot ファイル。
 # 通常、このファイルを編集する必要はないでしょう。
 # 設定の変更は (このディレクトリにある) OMakefile に記述するべきです。
 #
 # このファイルを編集するのであれば、
 # OMakefile とまったく同じ文法で記述してください。
 #

 #
 # (OMake と共に) インストールされている設定ファイルを読み込みます。
 # 使わないファイルがあれば削除することもできますが、
 # おそらくこの共通ファイルを編集したいとは思わないでしょう。
 #
 open build/C
 open build/OCaml
 open build/LaTeX

 #
 # 標準の設定ファイルを読み込んだ後、コマンドライン変数を再定義します。
 #
 DefineCommandVars()

 #
 # このディレクトリにある OMakefile を読み込みます。
 #
 .SUBDIRS: .

.. index::
   pair: OMakeroot; ライセンス
   pair: OMakefile; ライセンス

長いように見えて、大半はコメントです。先頭にはライセンスが記述されています。 ``--install`` オプションで生成されるファイルは MIT ライセンスであり、プロジェクトへの組み込み時のライセンスについて心配する必要はありません。

.. index:: open

``open ...``
^^^^^^^^^^^^

``open`` は他の OMake ソースファイル (拡張子が ``.om``) を読み込み、そのファイルで定義された変数や関数 (後述します) を使えるようにします。指定したファイルはいくつかのパスから検索されます。デフォルトでは ``open`` を記述した OMake ソースファイルのあるパスと (``.`` としたほうがわかりやすいでしょうか) 、 OMake の標準ライブラリのパスです。

ここで読み込まれているのは、標準ライブラリの ``build/C`` 、 ``build/OCaml`` 、 ``build/LaTeX`` の三つです。名前の通り、それぞれ C 、 OCaml 、 LaTeX のための便利な関数などが定義されていますが、必要なければこれらの行を削除しても問題ありません。

``open`` についてさらに詳しく知りたければ、 :ref:`ファイルのインクルード <IncludingFiles>` を参照してください。


.. index:: DefineCommandVars(), コマンドライン変数

``DefineCommandVars()``
^^^^^^^^^^^^^^^^^^^^^^^

``DefineCommandVars()`` は **コマンドライン変数** を読み込む **関数** です (OMake では関数が使え、定義もできます。以降、少しずつ触れていきます) 。コマンドライン変数は「変数=値」の形で渡されるコマンドライン引数のことで、この関数を呼ぶと再定義されます。実はわざわざこの関数を使わなくてもコマンドライン変数は定義されますが、変数によっては前述の ``open`` したファイル内で上書きされてしまうために、 ``open`` 後に ``DefineCommandVars()`` で再定義しています (まだ意味がわからなくても問題ありません) 。詳しくは :ref:`コマンドラインで変数を定義する <DefineCommandVars>` を参照してください。


.. index:: .SUBDIRS

``.SUBDIRS: .``
^^^^^^^^^^^^^^^

``.SUBDIRS`` はサブディレクトリの ``OMakefile`` ファイルを読み込む特殊なルールです。 ``.`` はカレントディレクトリを表しており、 ``.SUBDIRS: .`` は ``OMakeroot`` ファイルのあるディレクトリの (つまりルートディレクトリの) ``OMakefile`` ファイルを読み込みます。詳しくは :ref:`サブディレクトリの OMakefile ファイルを読み込む <LoadSubdirectories>` を参照してください。


まとめ
======

* ``omake --install`` コマンドで ``OMakeroot`` ファイルと ``OMakefile`` ファイルのテンプレートを生成できる。

.. note:: **ある対話**

   |
   | **M**: Makefile に似た OMakefile とか make との違いがどうとか、 make 知らないといけませんか？
   | **S**: 知ってるほうが文法的にはとっつきやすいでしょうが、 make の知識が足を引っ張ることもあったり。知ってるほうがいいけど知らないほうがいいです。
   | **M**: どっちですか。
 
