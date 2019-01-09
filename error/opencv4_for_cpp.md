# C++をコンパイル時にopencvが見つからない(opencv4)

`brew file update` をしていたため、brewで入れたソフトが全てアップデートされており、opencvのバージョンが `4.0.1` に上がっていた  
そのため、opencvのディレクトリ構造が変化していたため、コンパイル時にうまく読み込めていなかった  

    /usr/local/include/opencv
    /usr/local/include/opencv2
    -> /usr/local/include/opencv4  <-new!

また、 `opencv4` からC++コンパイラのバージョン `11` が必須になっている。

コンパイルする際のコマンドは以下のようになる

    g++ @@@.cpp -o @@@ -std=c++11 `pkg-config --cflags --libs opencv4`
    
今回、 `opencv3.x` を使用したかったのでdowngradeしないといけなくなった  
brewは一度入れたバージョンはキャッシュとして取ってあるのでそれを再インストールする  

    brew info opencv  # 入っているバージョンを確認
    brew switch opencv 3.4.3_2  # 過去のバージョンを再インストール
    
そのままコンパイルするとエラーがでる  

    dyld: Library not loaded: /usr/local/opt/openblas/lib/libopenblasp-r0.3.4.dylib
      Referenced from: /usr/local/opt/opencv/lib/libopencv_core.3.4.dylib
      Reason: image not found
      
どうやら `usr/local/opt/openblas` というライブラリが読み込め無いらしい  
バージョンが違うっぽいのでこれもbrewで過去のバージョンに戻す  

    brew switch openblas 0.3.4
    
この状態で、以下のようにコンパイルすると通る  

    g++ @@@.cpp -o @@@ -std=c++11 `pkg-config --cflags --libs opencv`
    
## 参考文献
[OpenCV(C++)の画像処理メモ - Qiita](https://qiita.com/yoyoyo_/items/aada199371f6802bb887)  
[OpenCVインストール構成の変遷（OpenCV 3.4.3，3.4.4，4.0.0） - Qiita](https://qiita.com/dandelion1124/items/eae435e601fba2adb0d1)  
[Setting up OpenCV and C++ development environment in Xcode for Computer Vision projects](https://medium.com/@jaskaranvirdi/setting-up-opencv-and-c-development-environment-in-xcode-b6027728003)  
[Homebrewで旧バージョンをインストールする方法(brew versionsはもう使えない) - Qiita](https://qiita.com/honeniq/items/778cc08d2db78e6774d8)
