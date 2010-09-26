.. _Doukaku:

========================
付録 C: どう書く？ OMake
========================

.. index:: FizzBuzz

FizzBuzz
========

このコードは `FizzBuzz - 逆引き OMake <http://unicus.ddo.jp/omake-wiki/index.php?FizzBuzz>`_ を元にしています。

::

  fizzbuzz() =
    i = 1
    line = ""
    while $(lt $i, 100)
      line = ""
      if $(equal $(mod $i, 3), 0)
        line = $(line)"Fizz"
        export
      if $(equal $(mod $i, 5), 0)
        line = $(line)"Buzz"
        export

    if $(not $(or $(equal $(mod $i, 3), 0), $(equal $(mod $i, 5), 0)))
      line = $i
      export
    echo $(line)
    i = $(add $i, 1)


.. index:: Brainfuck インタプリタ

Brainfuck インタプリタ
======================

このコードは `OMake がチューリング完全であることを示す - 逆引き OMake <http://unicus.ddo.jp/omake-wiki/index.php?OMake%E3%81%8C%E3%83%81%E3%83%A5%E3%83%BC%E3%83%AA%E3%83%B3%E3%82%B0%E5%AE%8C%E5%85%A8%E3%81%A7%E3%81%82%E3%82%8B%E3%81%93%E3%81%A8%E3%82%92%E7%A4%BA%E3%81%99>`_ を元にしています。

