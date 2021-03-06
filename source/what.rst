.. _WhatIsOMake:

====================
2 章: OMake とは何か
====================

OMake とは何か
==============

OMake はスケーラビリティと移植性に優れた汎用ビルドツールです。次の特徴があります。

親しみやすい文法
  OMake で用いるファイルの文法は、 make の文法とシェルスクリプトを合わせたようなシンプルなものです。すぐに慣れるでしょう。ただ、 make に似た文法で make 以上のことをやるためにややこしくなっている部分もあるので、思うように使いこなすのは案外厄介です。親しみやすい、ってだけで。

継続監視ビルド

スケールする
  OMake は大規模開発にもスケールします。

強力な依存解析
  ただ、ビルドしたいファイルの依存解析処理がデフォルトで用意されているか、誰かがすでに実装していればの話ですが。

移植性
  OMake では OSH シェルという独自のシェルを主に使います。 OSH シェルは OMake がサポートするすべてのプラットフォーム (Windows を含む) で動くので、ビルドファイルを複数のプラットフォームで同じように動かせます (あくまでシェルのみの話) 。しかし OSH シェルが基本となると、これはこれで困ることもあるので喜ばしいのかどうかわかりません。

拡張可能
  OMake はプログラミング言語でもあり、複雑なビルドを記述できます。新しい言語やツールに対応するルールを用意してライブラリにしておくこともできます。


.. note:: **ある対話**

   |
   | **N**: これ OMake の本だと思ったけど遠回しに dis ってんの？
   | **S**: メリットもあればデメリットもあるというか、メリットだと思ってたらデメリットのほうが大きい気がしてきた面もあるというか...


OMake が役に立つ状況
====================

次に OMake が役に立つと思われる状況を紹介します。

C または OCaml のソースコードが中心のプロジェクト
-------------------------------------------------

OMake には C と OCaml 向けのビルド設定が既に組み込まれており、手軽にプロジェクトのビルドファイルを用意できます。ただし C にしろ OCaml にしろ、組み込みのビルド設定だけですべてまかなえるほど充実しているとは言えません。

この他に C++ 向けのビルド設定も組み込まれていますが、筆者が C++ に明るくないため評価ができません。


大量または複雑な ``Makefile`` で構成されるプロジェクト
------------------------------------------------------

複雑な ``configure`` スクリプトのあるプロジェクト
-------------------------------------------------

大量のビルド対象ファイルがあるプロジェクト
------------------------------------------


OMake が向かない (かもしれない) 状況
====================================

上記に合わせて、 OMake が向かない (かもしれない) 状況も紹介します。こうして見ると、 OMake でなければならない状況はそれほどないのかもしれません。まあ適材適所ですね。


IDE がある
----------

特にプロプライエタリなプラットフォームを対象にしたプログラムを開発する場合は、用意された IDE を使うほうがずっと簡単でしょう。


ビルドするソースコードと同じ言語で実装されたビルドツールが既にある
------------------------------------------------------------------

最近は特定の言語向けにビルドツールを開発する傾向があるようです。例えば Java なら Ant や Maven 、 Ruby なら Rake などのビルドツールが開発されています。これらのビルドツールはビルド対象を限定するものの (汎用的に使えるものもあります) 、その分 IDE 並みに便利になる場合も多いです。

特定の言語に合わせたビルドツールの選択肢があるなら、できるだけそちらを優先するとよいでしょう。何せビルドツールとビルド対象の言語が同じですから、ビルドツールの開発者は何をどう作ればいいのか勝手を知っています。


ライセンス
==========

OMake に含まれるファイルには、 GPL ライセンス と MIT ライセンスのものが混ざっています。ビルドを行う OMake エンジンは GPL ライセンス、あらかじめ用意されているビルドファイルは MIT ライセンスで配布されています。 OMake エンジンのソースコードに手を入れない限り、 GPL の影響はありません。


OCaml との関係
==============

OMake は OCaml で実装されていますが、 OMake を使うのに OCaml の知識は必要ありません。また、 OMake は OCaml ソースファイル専用のビルドツールでもありません (OCaml ソースファイル **も** ビルドできます) 。本書では「 :ref:`OCaml ソースファイルのビルド <BuildingOCamlSourceFiles>` を除き、 OCaml についてこれ以上は触れません。

.. note:: **ある対話**

   |
   | **O**: でも OCaml のインストールは必要なんですよね。
   | **S**: はい。しかも最新の OCaml を使うと OMake 側に修正が必要というオマケつき。詳しくは次で。


インストール
============

:ref:`付録 A: ビルドとインストール <BuildAndInstall>` を参照してください。

