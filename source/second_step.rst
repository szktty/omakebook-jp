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


処理の流れ
==========

* OMakeroot の探索
* OMakefile のロード。上から順に評価
* 依存関係グラフの生成、静的ルールの定義
* ターゲットルールの実行

依存関係グラフは動的に更新される


``hello.c`` の例


ルールと関数の評価順序
======================


