# Alacrittyでitem2のように画面分割が使いたい！
これは、[琉大 Advent Calendar 2020](https://adventar.org/calendars/5231)の17日目の記事です。

これまでターミナルエミュレータはterminal.appやiterm2、hyperなどを使ってきましたが、最近はAlacrittyで作業をしています。  
Alacrittyは爆速なのですが、如何せんそのミニマルな設計から画面分割や新規タブの作成が行えないため不便だと感じやすく、新規参入者を減らしてしまっているような気がします。  
そこで、今回はTmuxと組み合わせてiterm2風なキーバインドで画面分割や新規タブの作成が行えるようにしたいと思います。


## 使用しているもの
- Alacritty
- Tmux
- zsh

## Alacritty
Rustで書かれたターミナルエミュレータです。  
最小限な機能で爆速に動作することを目的に作られています。  
そのため、画面分割機能や新規ウィンドウ作成機能はありません。  

でもiterm2のような縦横の画面分割機能は欲しいので、[この記事](https://arslan.io/2018/02/05/gpu-accelerated-terminal-alacritty/)を~~ほぼ丸パクリ~~参考にTmuxを使っていい感じに画面分割できるようにします。

Macユーザーであればhomebrewを使って簡単にインストールすることができます。

```shell
$ brew cask install alacritty
```

自分でBuildしたい場合は[AlacrittyのINSTALLガイド](https://github.com/alacritty/alacritty/blob/master/INSTALL.md)を参考にするといいと思います。

## Tmux
Terminal Multiplexerの略で、1つのターミナル上で複数のターミナルを開けるものです。  
ターミナルを終了してもセッションが保持されるので、サーバーとの接続を維持したいときに便利だとは聞きます。  
似たようなモノで[screen](https://www.gnu.org/software/screen/)というものがあるらしいのですが、使ったことがないのに加え、Alacrittyの記事では大体がTmuxについて書かれていたので今回はTmuxを使います。

こちらもMacユーザーであればhomebrewを使って簡単にインストールすることができます。

```shell
$ brew install tmux
```

## Alacritty + Tmuxの設定
ここからはAlacrittyとTmuxの設定をしていきます。

まず、Tmuxの設定から行います。  
Tmuxはデフォルトのプレフィックスキーが `Ctrl+b` になっており、macのショートカットキーやemacsを使用している人には辛いです。  
自分的には `Ctrl+q` はあまり使っていないのでキーの割り当てを `Ctrl+b` から `Ctrl+q` に変更します。

何かしらのエディタを使って `~/.tmux.conf` ファイルを開きましょう（ない場合は作成します）。  
以下の設定を書き込みます。

```
# ~/.tmux.conf
set -g prefix C-q  # Ctrl-qをプレフィックスキーに割り当て
unbind C-b         # Ctrl-bの割り当てを解除
```
ひとまずこれで `Ctrl+b` がtmuxに奪われなくなりました。

次に、Alacrittyの方の設定を行っていきます。  
Alacrittyには（もちろん）設定用のリッチなUIはなくyamlファイルに設定を書いていきます。  
ファイルは `~/.config/alacritty/alacritty.yml` です。ない場合には[リリースページ](https://github.com/alacritty/alacritty/releases)のAssetsからダウンロードして配置してください。  
このalacritty.ymlファイルを編集することでフォントやカラーを変えたりログインシェルを変えたりすることができます。  
今回はTmuxと組み合わせるためにキーバインドの変更を行います（ついでにログインシェルも変更します）。

### キーバインドの変更
最初にも書いたようにAlacrittyには新しくタブを作ったり画面を分割したりすることができません。  
そこで、Tmuxのペインやセッションを使ってタブ作成、画面分割の機能を再現していきます。  

Tmuxではプレフィックスキーに加え、`c` で新規ウィンドウ（iterm2でのタブ）作成、 `%` で左右にペインの分割（横分割）、 `"` で上下にペインの分割（縦分割）が行えます。これらをそれぞれ `Cmd+t`、 `Cmd+d`、 `Cmd+Shift+d` に割り当てます。  
alacritty.ymlファイルに以下のように追記します。  
（下の例では、ついでに `Cmd+w` でペインを削除するようにしたり、￥を\に変更したりしています）

```yml
...
key_bindings:
...
  - { key: T,      mods: Command,             chars: "\x11\x63"   }
  - { key: D,      mods: Command,             chars: "\x11\x25"   }
  - { key: D,      mods: Command|Shift,       chars: "\x11\x22"   }
  - { key: W,      mods: Command,             chars: "\x11\x78"   }  # Cmd+wでペインを削除する
  - { key: Yen,                               chars: "\x5c"       }  # ¥を\に変更
```
Alacrittyでは、キー入力（`chars`）は8進数のasciiコードを与える必要があるため、自分で追加したい場合には[ASCII table](https://www.ascii-code.com/)などを参考にするといいと思います。

これでAlacritty上でTmuxを使って画面分割、新規タブの作成ができるようになりました。  
次はログインシェルを変更していきます。

## ログインシェルの変更
私は最近zshからfishに乗り換えたのですが、ログインシェルをfishにするのはfishに何かあったときにalacrittyが開かなくなることが怖いので、`Alacritty -> zsh -> Tmux -> fish` の順で起動するという、とても面倒なことをしています（別にAlacrittyでfishを開いてtmuxを起動するようにしてあげればいいのだけれど...）。  
以下ではAlacrittyのログインシェルにzshを指定して、zshからTmuxを起動するように設定を行います。

```yml
...
shell:
  program: /bin/zsh
  args:
    - --login
```

zshからTmuxを起動するためには、 `~/.zshrc` に以下を追記します。

```shell
# launch tmux when start zsh
if command -v tmux &> /dev/null && [ -z "$TMUX" ]; then
    exec tmux
fi
```

## 終わりに
これで一通り満足できる環境ができました。  
できればtmuxのウィンドウ表示やAlacrittyのカラースキームをいろいろ弄りたいのですが、「そんなことより研究しなよ」と心の声が聞こえるのでまた次回にでも書こうと思います。

## 参考文献
- [http://www.tohoho-web.com/ex/tmux.html](http://www.tohoho-web.com/ex/tmux.html)
- [https://qiita.com/nmrmsys/items/03f97f5eabec18a3a18b](https://qiita.com/nmrmsys/items/03f97f5eabec18a3a18b)
- [https://blog.nijohando.jp/post/gpu-accelerated-terminal-emulator-alacritty/](https://blog.nijohando.jp/post/gpu-accelerated-terminal-emulator-alacritty/)
- [https://arslan.io/2018/02/05/gpu-accelerated-terminal-alacritty/](https://arslan.io/2018/02/05/gpu-accelerated-terminal-alacritty/)
- [https://www.tecmint.com/tips-for-tmux-terminal-sessions/](https://www.tecmint.com/tips-for-tmux-terminal-sessions/)
