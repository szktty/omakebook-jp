.. _Troubleshooting:

=============================
16 章: トラブルシューティング
=============================

.. index:: 継続監視ビルド; 再ビルドされない

.. _PollingTrouble:

継続監視ビルドの実行中、ファイルの変更が反映されない
====================================================

``Fatal error: exception Invalid_argument("FAM not enabled")``
--------------------------------------------------------------

``omake -P`` を実行してこのメッセージが表示されたら、 `FAM (File Alteration Monitor) <http://oss.sgi.com/projects/fam/index.html>`_ をインストールしてください。パッケージ管理システムがあれば、 ``libfam`` または ``libgamin`` のパッケージでインストールできます。


途中から再ビルドされなくなる
----------------------------

継続監視ビルドでは ``OMakefile`` の変更も反映されますが、このとき変更後の ``OMakefile`` にエラーがあると、以降再ビルドされなくなることがあります。ファイルを変更しても ``*** omake: polling for filesystem changes`` と表示されるだけの状態です。こうなったらコマンドを終了し、継続監視ビルドをやり直してください。

