# hyper (terminal)を使ってみた
1年間itermを使ってきたんですが、最近(ちょっと前から？)人気な[hyper](https://hyper.is/)が気になっていたので少し使ってみました。

## 参考
[【Hyper】良い感じのターミナル環境を構築 - Qiita](https://qiita.com/shidash/items/ca60307a1341086b6e44)  
[おしゃれなターミナルHyperのテーマ変更、プラグイン追加、日本語化するための手順](https://www.virment.com/how-to-setup-hyper-terminal/)  
[hyper(hyperterm) 2.0 を日本語対応する - Qiita](https://qiita.com/terrierscript/items/47b11b8a24438d789210)  

## アジェンダ
- install方法
- 設定ファイル
- plugin
- 使ってみた感想

## install方法
macを使っているので `Homebrew` を使ってinstall
```
brew cask install hyper
```
これだけですw

## 設定ファイル
hyperの設定ファイルは`~/.hyper.js`になります。  
とりあえず日本語設定は必要と思われるので以下のように設定
```
// for environment variables
env: {
    LANG: 'ja_JP.UTF-8',
    LC_ALL: 'ja_JP.UTF-8'
},
```
もちろんフォントやフォントサイズなどを変更することもできます
```
// default font size in pixels for all tabs
fontSize: 12,

// font family with optional fallbacks
fontFamily: 'Menlo, "DejaVu Sans Mono", Consolas, "Lucida Console", monospace',
```

## plugin
pluginは<https://github.com/bnb/awesome-hyper>にあるので、好きなものを探して入れてみる。  
入れたいプラグインを以下のように記述する
```
plugins: [
    'hyper-altair',
    'hyper-search',
    'hyper-statusline',
    'hyper-tab-icons',
    'hyperterm-visor',
    'hypercwd',
],
```
### プラグインの説明
- [hyper-altair](https://www.npmjs.com/package/hyper-altair)  
hyperのthemeです。他にもたくさんのthemeがあります  
- [hyper-search](https://www.npmjs.com/package/hyper-search)  
hyperの中で検索が使えるようになります(ファイル編集中は少しおかしいところにカーソルが行ったりする...)  
- [hyper-statusline](https://www.npmjs.com/package/hyper-statusline)  
hyperのウィンドウ下部にディレクトリ名とgitの状態をスタイリッシュに表示してくれる(これのおかげでshellの設定ファイルがスッキリするかも)  
- [hyper-tab-icons](https://www.npmjs.com/package/hyper-tab-icons)  
hyperのtabにshellの状態をiconで表示してくれる、格好いい  
- [hyperterm-visor](https://www.npmjs.com/package/hyperterm-visor)  
hyperをitermのようにhotkeyでhide/showを切り替えられる(説明あってるかな)  
- [hypercwd](https://www.npmjs.com/package/hypercwd)  
新しいtabで開くときに同じディレクトリになる

## 使ってみた感想
Electron製なので、重いのかな？と思いつつ始めたhyperですが、結構軽快に動いています。  
ただ、vimなどでファイルを開くとitermと比べてかなり動作がもっさりします...  
あと、プラグインでカスタマイズをするととても綺麗なCUI環境(これはもうGUIだが)を整えることができるのがいいですねw  
まだ使い始めて3日くらいなので、これからカスタマイズなどをして使いやすくしていきたいですね。  
