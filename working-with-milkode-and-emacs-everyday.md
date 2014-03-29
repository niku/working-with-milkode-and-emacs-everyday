# 毎日milkodeとemacsをこんな風に使ってます

author
   niku
date
   2014/03/29
allotted-time
   5m

# ソースコード

-   書く時間と検索する時間どんな比率ですか？
-   僕はだんだん検索する時間が長くなってきた
    -   (他人のコード)フレームワークでどうなってるんだっけ？
    -   (自分のコード)こんなの書いたことある気がする

# ソリューション

grep

# grepの悩み(1)

-   検索不要なディレクトリがあるなあ(.git以下とか)
-   プロジェクト単位で検索したいなあ
-   hogeを検索すると多すぎる => hogeかつfugaのものってどうやるんだっけ？(and検索)

# grepの悩み(2)

- 探してみると「できる」のはわかった
- 「簡単に」やりたかった

# grepの悩み(3)

-   一部はsilver-searcher(ag)で解決した
-   全部Milkodeで解決した

# Milkode

<http://milkode.ongaeshi.me/>

-   無視したいパス/ファイル指定できる(デフォルトでもある程度整っている)
-   プロジェクト単位での検索
-   条件を増やすとand条件になる

# 消し方

Milkodeは綺麗に消せる．まずは試そう!

    $ rm -rf ~/.milkode
    $ gem uninstall milkode

# 準備 - Milkodeのインストール

    $ gem install milkode
    Successfully installed milkode-1.5.0
    1 gem installed
    $ milk --version
    milk 1.5.0

# 準備 - MilkodeのDBを初期化

    $ milk init --default
    create     : /Users/niku/.milkode/milkode.yaml
    create     : /Users/niku/.milkode/db/milkode.db created.

# 準備 - MilkodeのDBにデータを入れる

    $ cd ~/projects
    $ find . -maxdepth 1 -type d ! -name '.' | xargs milk add
    result     : 160 packages, 38627 records, 38627 add. (15m 13.99s)
    *milkode*  : 160 packages, 38627 records in /Users/niku/.milkode/db/milkode.db.

# 準備できた

# コマンドから - こんな風に出ます

    $ cd ~/project/ruby
    $ gmilk rb_ascii8bit_encoding
    (略)
    strftime.c:219:     enc == rb_ascii8bit_encoding() || enc == rb_locale_encoding())) {
    string.c:489:   to == rb_ascii8bit_encoding()) {
    string.c:542:   rb_enc_associate(str, rb_ascii8bit_encoding());
    string.c:2122:      rb_enc_associate(str1, rb_ascii8bit_encoding());
    string.c:4637:  enc = rb_ascii8bit_encoding();
    win32/win32.c:1964:     if (utf16 == rb_ascii8bit_encoding())
    win32/win32.c:2049:    if (enc == rb_ascii8bit_encoding())

# コマンドから - プロジェクトの中から探す

デフォルトで今いるディレクトリ(を含んでいるプロジェクト)を意識してます

    $ cd ~/project/ruby
    $ gmilk hoge | wc -l
    304
    $ cd ~/project/ruby/misc
    304

# コマンドから - AND条件で絞り込み

複数条件を書くだけ
"hoge"と"fuga"が同じ行に含まれているもの

    $ cd ~/project/ruby
    $ gmilk hoge | wc -l
    2222
    $ gmilk hoge fuga | wc -l
    329

# コマンドから - 拡張子で絞り込み

-s "拡張子名"
拡張子がcのもの

    $ cd ~/project/ruby
    $ gmilk hoge | wc -l
    2222
    $ gmilk hoge -c | wc -l
    13

# コマンドから - 全てのプロジェクトから探す

-a をつける

    $ cd /tmp
    $ gmilk hoge fuga -a | wc -l
    86

# コマンドから - DBの更新

ソースを新しくしたらDBを明示的に更新する

    $ milk update --all

検索するときに自動更新する -u コマンドもあるみたい(試していない)

    $ gmilk -u hoge

# Emacsから - 入れる

<https://github.com/ongaeshi/emacs-milkode/blob/master/milkode.el>

ダウンロードしてEmacsのパスの通っているところに置きます

(TODO:Emacsのパッケージ管理システムへ登録しよう)

# Emacsから - 設定する

<https://github.com/niku/.emacs.d/blob/master/inits/26-milkode.el>

    (require 'milkode)
    (global-set-key (kbd "M-g M-m") 'milkode:search-from-all-packages)

# Emacsから - 動かす

Emacs上で `M-g M-m` して，ミニバッファにコマンドを入力する．
