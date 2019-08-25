# Rust環境をDockerで作成した

DockerでRustの環境を作る際に、なんやかんや色々ハマってしまったので備忘録として。  
docker内でのコマンドは `(docker)$` って感じで書いています。

あと、便利に使えるようにzsh用のコマンド化したコードも載せています。

## 作成環境

```
MacBookPro == 2017 Core i5 16GB
Docker desktop == 2.1.0.1
```

## 結果
- Dockerfile
// TODO: gistのURLを貼ればいいかな

- zshコマンド
```
function cargo {
    case $1 in
        (new)
            docker run --rm -t -v $PWD:/work rust:osx cargo new $2 $3;;
        (run)
            docker run --rm -t -v $PWD:/work rust:osx cargo run $2 $3;;
        (build)
            docker run --rm -t -e CROSS_TRIPLE=x86_64-apple-darwin -v $PWD:/work rust:osx cargo build --target=x86_64-apple-darwin $2;;
    esac
}
```

## ハマったところ

### 普通にRustの公式Dockerイメージをpullしてきても、ベースがlinuxなのでLinux向けにコンパイルされてしまう
---

cargo runまでは別に動くんだけど、buildとかしても作成されるバイナリはlinux用なのでホスト側（Mac）で実行できない...orz  
そこで、cross compileができるイメージを探したところ、[この記事](https://mzp.hatenablog.com/entry/2017/05/02/122348)の方が書いたDockerfileを参考に（ほぼ写経）して作成した。

```
$ docker run --rm -it rust:osx bash
(docker)$ CROSS_TRIPLE=x86_64-apple-darwin
(docker)$ crossbuild cargo --target=x86_64-apple-darwin
```
これで無事にMacで動くバイナリのbuildができるようになった。

### `CROSS_TRIPLE=x86_64-apple-darwin` この環境変数を書き込んでいる箇所をDockerfileの中に埋めようと思ったが、うまくいかない
---

原因が特定できていないが、 `CROSS_TRIPLE=x86_64-apple-darwin` という環境変数をDockerfileに下のように設定しても、うまくいかずにハマった。

```
ENV CROSS_TRIPLE=x86_64-apple-darwin
...
$(docker) echo $CROSS_TRIPLE
# x86_64-apple-darwin14
```
！？  
なぜかx86_64-apple-darwin **14** になってしまう。

ARGからENVに落とし込んだり、無理やり設定しようとしたがうまくいかなかった。  
`docker run CROSS_TRIPLE=x86_64-apple-darwin crossbuild cargo --target=x86_64-apple-darwin` こんな感じで無理やり設定しようともした。  

結果的に、Dockerfileに書くのを諦めて、以下のように `docker run -e` で設定して、 `cargo build` するとうまくいった。

```
docker run -e CROSS_TRIPLE=x86_64-apple-darwin cargo --target=x86_64-apple-darwin
```