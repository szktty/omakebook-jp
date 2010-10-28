.. _SecondStep:

==============
4 章: 次の一歩
==============

インストールされる ``OMakeroot`` ファイルの内容
===============================================

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
------------

``open`` は他の OMake ソースファイル (拡張子が ``.om``) を読み込み、そのファイルで定義された変数や関数 (後述します) を使えるようにします。指定したファイルはいくつかのパスから検索されます。デフォルトでは ``open`` を記述した OMake ソースファイルのあるパスと (``.`` としたほうがわかりやすいでしょうか) 、 OMake の標準ライブラリのパスです。

ここで読み込まれているのは、標準ライブラリの ``build/C`` 、 ``build/OCaml`` 、 ``build/LaTeX`` の三つです。名前の通り、それぞれ C 、 OCaml 、 LaTeX のための便利な関数などが定義されていますが、必要なければこれらの行を削除しても問題ありません。

``open`` についてさらに詳しく知りたければ、 :ref:`ファイルのインクルード <IncludingFiles>` を参照してください。


.. index:: DefineCommandVars(), コマンドライン変数

``DefineCommandVars()``
-----------------------

``DefineCommandVars()`` は **コマンドライン変数** を読み込む **関数** です (OMake では関数が使え、定義もできます。以降、少しずつ触れていきます) 。コマンドライン変数は「変数=値」の形で渡されるコマンドライン引数のことで、この関数を呼ぶと再定義されます。実はわざわざこの関数を使わなくてもコマンドライン変数は定義されますが、変数によっては前述の ``open`` したファイル内で上書きされてしまうために、 ``open`` 後に ``DefineCommandVars()`` で再定義しています (まだ意味がわからなくても問題ありません) 。詳しくは :ref:`コマンドラインで変数を定義する <DefineCommandVars>` を参照してください。


.. index:: .SUBDIRS

``.SUBDIRS: .``
---------------

``.SUBDIRS`` はサブディレクトリの ``OMakefile`` ファイルを読み込む特殊なルールです。 ``.`` はカレントディレクトリを表しており、 ``.SUBDIRS: .`` は ``OMakeroot`` ファイルのあるディレクトリの (つまりルートディレクトリの) ``OMakefile`` ファイルを読み込みます。詳しくは :ref:`サブディレクトリの OMakefile ファイルを読み込む <LoadSubdirectories>` を参照してください。


変数と値
========

make に似せた文法のせいもあって、 OMake の変数と値にはちょっと癖があります。ただし OMake の達人になる必要はありませんから、あまり深く考えるのはやめましょう。


変数
----

変数を宣言するには、何かしらの値を代入します。宣言した変数は ``$(変数名)`` で参照できます。

::

  # 代入兼宣言
  X = 1
  
  # 参照
  echo $(X) # 1 と表示される

**ルール内では変数の宣言・代入ができません** 。正確には代入文はエラーになりませんが、変数に反映されません。ルール外か関数定義内でのみ変数の宣言・代入を行えます。つまり、ルール内で参照する変数はルール外で定義済みのものに限られます。依存関係の解析上、ルール内で動的な処理が行われると困る (依存関係が変わってしまう) からでしょう。

変数スコープは動的です。 OMake の変数スコープは完全に理解するとなると面倒くさいので、最低限のポイントだけ押さえておきます。

* ブロック外の変数は何もしなくても参照できます。

* 変数の内容を変更しても、そのままではブロック外に影響しません。

* 変数をどのファイルからでも参照できるようにするには、 ``global`` を指定して宣言します (グローバル変数) 。

  ::

    global.X = 1



値
--

関数定義
========


サブディレクトリの扱い
======================


処理の流れ
==========

* OMakeroot の探索
* OMakefile のロード。上から順に評価
* 依存関係グラフの生成、静的ルールの定義
* ターゲットルールの実行

依存関係グラフは動的に更新される


``hello.c`` の例


