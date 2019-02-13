# Octave GUIのインストール方法
octave GUIと検索してもGUIソフトの入れ方が古いようで何度もhomebrewに怒られてしましました。

基本的に参考にしたのは以下の記事です。  
[macにoctaveのインストール 書き中~](https://qiita.com/miyamotok0105/items/467ea9f4d22cdcdbb103)  
[Mac OS X に GNU Octave をインストールする](https://blog.amedama.jp/entry/2017/04/28/232547)

上記のやり方だと、homebrewにhomebrew/scienceというtapは空と言われます。

    $ brew tap homebrew/science
    Error: homebrew/science was deprecated. This tap is now empty as all its formulae were migrated

素直に[公式のページ](https://octave-app.org/)を見るとインストール方法が載っていたので、公式にしたがってインストールを進めます。

今回は、homebrewを使ってインストールを行いました。

環境
- macOS Mojave 10.14.2
- homebrew 2.0.1

インストール手順

    $ brew tap octave-app/octave-app
    $ brew cask install octave-app

以上。  
ただ、GUIのOctaveを使ってみると、日本語が文字化けしており、未だ解決できていません...
フォント変えたりしたけど解決策がわからない。

解決できたら追記する予定です。
