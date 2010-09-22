.. _BuildAndInstall:

============================
付録 A: ビルドとインストール
============================

現在 OMake の最新バージョンは 0.9.8.5 です。ソースコード及びバイナリは `Download OMake <http://omake.metaprl.org/download.html>`_ からダウンロードできます。開発版 (0.9.8.x) は後述する問題点が修正されていますが、安定版を使うべきです。


ソースコードからのビルドとインストール
======================================

OCaml のインストール
--------------------

OMake をインストールする前に、 OCaml をインストールしておきます。 OCaml のバージョンは最新のもので構いませんが、ソースコードを多少修正する必要があります。


ソースコードの修正
------------------

OMake の最新バージョンのリリース日時は少し古く、最新の OCaml を使うとそのままではビルドできません。次のファイルを修正すればビルドできるようになります。


``src/exec/omake_exec.ml``
^^^^^^^^^^^^^^^^^^^^^^^^^^

最新の OCaml では、次に示すコードをコンパイルできません。次のようにコメントアウトするか、削除します。

::

 external sync : unit -> unit = "caml_sync"

 ;; コメントアウト (または削除)
 (* external sync : unit -> unit = "caml_sync" *)


``src/libmojave-external/cutil/lm_printf.c``
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

メモリ解放に関するバグがあり、 gcc がエラーを出力します (どうも環境によってはエラーになることなくコンパイルできてしまうこともあるようです) 。修正内容は簡単で、次のように変数名を変更するだけで済みます。

::

   ;; 142 行目
    #endif
        if(code < 0) {
            if(bufp != buffer)
   -            free(buffer);
   +            free(bufp);
            failwith("ml_print_string");
        }
        v_result = copy_string(bufp);
        if(bufp != buffer)
   -        free(buffer);
   +        free(bufp);
        return v_result;
    }
    
   ;; 190 行目
    #endif
        if(code < 0) {
            if(bufp != buffer)
   -            free(buffer);
   +            free(bufp);
            failwith("ml_print_string");
        }
        v_result = copy_string(bufp);
        if(bufp != buffer)
   -        free(buffer);
   +        free(bufp);
        return v_result;
    }


ビルドとインストール
--------------------

次のコマンドを実行します。

::

 % make bootstrap
 % make all
 % make install


Solaris でのビルドとインストール
================================

