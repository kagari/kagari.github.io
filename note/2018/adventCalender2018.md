# RSA暗号方式をpythonで実装してみる
[琉球大学工学科知能情報コースAdventCalender2018](https://adventar.org/calendars/3436)の8日目の記事です。

こんにちは、[kagari](https://twitter.com/kagarin_ie)です！

私自身2回目の記事でAdvent Calendarに登録するなんて思ってもみませんでしたw  
記事の内容を「RSA暗号方式をpythonで実装する」って決めた理由としては、最近授業で習ったからというだけの理由です。

何か至らない点があっても暖かい目で見守ってくださいm(__)m

## RSA暗号方式ってなに？
RSA暗号とは、桁数が大きい合成数の素因数分解問題が困難であることを安全性の根拠とした公開鍵暗号の一つである。(wikiより)  
うん、完全に理解した(多分)  

要するに**素数を使った暗号方式**だね!  
今回はこれをpythonで実装していく

## アルゴリズム
### 鍵の生成
まずは公開鍵の生成を行う。  
2つの素数をp,qとし、n=p×qとm=(p-1)×(q-1) を求める。  
さらに、nとmとが互いに素である数$e$を定める。  
この(e,n)が公開鍵である。  

次に、秘密鍵の生成を行う。  
ed mod ((p-1)(q-1)) = 1となるようなdを見つける。  
このdが秘密鍵である。

公開鍵と秘密鍵を生成したらp,qは用済みなのでしっかり破棄しましょう。

### 暗号化と複合

#### 暗号化
平文(M)を暗号化すると、暗号文(C)は  
C=M^e mod nとなる。  
これで暗号化を行う。

#### 複合
複合は、暗号文(C)を以下のようにして平文(M)にすることができる。   
M=C^d mod n  
これで複合を行う。

## 実際に実装していく
早速、どうやってpとqを生成すればいいかわからないという問題にぶつかった。  
が、今回は全然セキュリティとかは考えていないので、手打ちにすることにする()

使用するライブラリはmathだけ!

    import math
    
とりあえず、公開鍵と秘密鍵を生成する関数を作ってみる。  
まずは公開鍵から
    
    def get_public_number(p, q):
        n = p*q
    
        # mと互いに素な数eを見つける
        m = (p-1)*(q-1)
        e = 2
        while math.gcd(m, e) != 1:
            e += 1
        return e, n
        
次に、秘密鍵を生成する関数を用意
    
    def get_private_number(e, p, q):
        d = 1
        while True:
            if (e*d) % ((p-1)*(q-1)) == 1:
                break;
        d+=1
        return d

 実際に生成してみる。
 
    p=3
    q=7
    
    e, n = get_public_number(p,q)
    d = get_private_number(e, p, q)
    
それぞれe,n,dを出力してみるとe=5, n=21, d=5になった  
これで暗号化と複合のための鍵が生成できた。

次は暗号化と複合を行う関数を実装する  
今回は平文をM、暗号文をCとする

    # 暗号化する関数
    def encryption(M, e, n):
        C = M**(e) % n
        return C

    # 複合する関数
    def composite(C, d, n):
        M = C**d % n
        return M

実際に暗号化、複合を行なってみる

    M = 3 # 平文
    C = encryption(M, e, n)
    # C = 12
    comp_M = composite(C, d, n)
    # comp_M = 3
    
できた！！

これで、RSA暗号方式の一通りの実装ができました。  
ただ、今回はp,qを自分で決めて鍵の生成を行い、さらにeは互いに素な数の中でもっとも小さな数を用いているので、このままでは全然暗号化としては役に立たなさそうですねw  

## まとめ
結構RSAの理論的(?)には簡単に実装することができました。  
ただ、p,q(大きな数の素数)の生成や暗号鍵の生成で技術・知識的に不足していると感じました...

次実装する時は(多分ない)もっと暗号として使えるように実装していきたいなー

何かアドバイスなどがあれば[Twitter](https://twitter.com/kagarin_ie)の方によろしくお願いします！

**ps**  
数式ってgithub pagesでどうかけばいいんや...