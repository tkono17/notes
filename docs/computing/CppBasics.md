# C++プログラミングの基礎

## C++プログラムの作成と実行

### 目標
* C++プログラムのソース・コードを書いてコンパイル・実行できるようになる。
* C++プログラムにデータを渡したり、結果を表示させられるようになる。

### Hello, world
最初のC++プログラムとして、Hello, world! という文字列を表示するだけのプログラムを作成する。ソースコードは以下のようになる。これをhello.cxxという名前のファイルに書いて保存しておく。
```c++: title="hello.cxx"
#include <iostream>

int main(int argc, char* argv[]) {
    std::cout << "Hello, world!" << std::endl;
    return 0;
}
```

### プログラムのコンパイルと実行
プログラムのソース・ファイル（hello.cxx）をコンパイルして実行可能ファイル（hello.exe）を作るにはg++コンパイラを使う。以下のようにg++にコンパイルしたいソース・ファイル名を引数で渡す。出力ファイル名は-oオプションで渡す（-o hello.exeの部分）。
```bash
g++ -o hello.exe hello.cxx
```
コンパイルができたら、hello.exeファイルができているか確認して、実行する。プログラムを実行するにはそのファイル名をコマンドとして入力して実行すればよい。以下の例では、このプログラムが現在の作業ディレクトリにあるものであることを明示して先頭に./付けている。
```bash
ls
./hello.exe
```

### プログラムと標準入出力
次に、hello.cxxを少し修正して、プログラムを実行するとユーザーの名前を聞くように変更する。ユーザーが名前を入力すると、その人に対して挨拶するようにする。完成後のプログラムは以下のように動作して欲しい。
```c++
./helloToYou.exe
>> What is your name? (ここにユーザーが名前を入力)
```
下の例では、ユーザーが質問の後にAliceと入力した場合。
```c++
./helloToYou.exe
>> What is your name? Alice
>> Hello, Alice!
```

```c++
#include <iostream>
#include <string>

int main(int argc, char* argv[]) {
    std::string name = "";
    std::cout << "What is your name? " << std::endl;
    std::cint >> name;
    std::cout << "Hello, " << name << "!" << std::endl;
    return 0;
}
```

このソース・コードをhelloToYou.cxxとして保存して、コンパイル・実行せよ。

### コマンドライン引数
上の例では、プログラムにユーザーの名前を渡すために質問を表示して、その答えを受け取るというように対話的にデータをやり取りした。どのようなデータを渡す必要があるか予め分かっている場合には、コマンドライン引数を利用することも可能である。

```c++
#include <iostream>
#include <string>

int main(int argc, char* argv[]) {
    std::string name = "";
    if (argc == 2) {
        name = argv[1];
    } else {
        std::cout << "Usage: " << argv[0] << " <name>" << std::endl;
        return -1;
    }
    std::cout << "Hello, " << name << "!" << std::endl;
    return 0;
}
```
このプログラムをhelloTo.cxxとして保存して、コンパイル・実行せよ。ただし、実行時に引数で名前を渡す必要がある。

## 素因数分解プログラム

### 目標
* C++プログラムで整数の演算をする。
* C++プログラムの制御構造（繰り返し、条件分岐）を使う。

### プログラムの分解

### 整数の演算

### 制御構造

### 素数判定プログラムの作成

### 繰り返し処理

### 関数

### 素因数分解プログラムの作成

## 予定表プログラム

### ウェブページとHTML

### HTMLとCSS

### 予定データの形式
```CSV
物理学実験,月,13:20,16:30
高エネルギー物理学特論,火,13:20,14:50
高エネルギー物理学演習,火,15:00,16:30
```


### STLコンテナ

### データ構造の導入

### 並べ替え


