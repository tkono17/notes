# C++プログラミングの基礎

## C++プログラムの作成と実行

### 目標
* C++プログラムのソース・コードを書いてコンパイル・実行する手順を学ぶ。
* C++プログラムにデータを渡したり、プログラムからデータを出力させられるようになる。

### Hello, world
最初のC++プログラムとして、Hello, world! という文字列を表示するだけのプログラムを作成する。ソースコードは以下のようになる。これをhello.cxxという名前のファイルに書いて保存しておく。

```c++ title="hello.cxx"
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

```c++ title="helloToYou.cxx"
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

```c++ title="helloTo.cxx"
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

コマンドライン引数で、2つの整数を渡してその和を計算するプログラムを書いてみる。

```c++ title="addNumbers.cxx"
#include <iostream>
#include <cstdlib>

int main(int argc, char* argv[]) {
  if (argc != 3) {
    std::cout << "Usage: " << argv[0] << "<number1> <number2>" << std::endl;
    exit(-1);
  }
  int a = std::atoi(argv[1]);
  int b = std::atoi(argv[2]);
  int c = a + b;
  std::cout << "Two numbers " << a << " and " << b << " were given" << std::endl;
  std::cout << "  --> " << a << " + " << b << " = " << c << std::endl;
  return 0;
}
```

```bash
> g++ -o addNumbers.exe addNumbers.cxx
> ./addNumbers.exe 3 15
```

```c++ title="calculate.cxx"
#include <iostream>
#include <cstdlib>

int main(int argc, char* argv[]) {
  if (argc != 4) {
    std::cout << "Usage: " << argv[0] << "<number1> <operator> <number2>" << std::endl;
    std::cout << "-------" << std::endl;
    std::cout << "Example: " << argv[0] << "31 + 22" << std::endl;
    exit(-1);
  }
  int a = std::atoi(argv[1]);
  char op = argv[2][0];
  int b = std::atoi(argv[3]);
  if (op == '+') {
    int c = a + b;
    std::cout << "Calculating " << a << op << b << std::endl;
    std::cout << "  --> " << a << " " << op << " " << b << " = " << c << std::endl;
  } else {
    std::cout << "Unknown operator " << "'" << op << "'" << std::endl;
  }

  return 0;
}
```

このプログラムは次のようにコンパイル・実行できる。
```bash
> g++ -o calculate.exe calculate.cxx
> ./calculate.exe 3 + 15
```

このプログラムを改良して、足し算だけでなく、以下のように引き算、掛け算、割り算もできるようにせよ。
```bash
> ./calculate.exe 10 - 6
  --> 4 # と表示させたい
> ./calculate.exe 10 * 6
  --> 60 # と表示させたい
> ./calculate.exe 50 / 6
  --> 8 ... 2 # と表示させたい
```

## 素因数分解プログラム

### 目標
* C++プログラムで整数の演算をする。
* C++プログラムの制御構造（繰り返し、条件分岐）を使う。

### プログラムの分解

以下のようにコマンドライン引数で整数を与えると、素因数分解をしてその因子を全て表示するプログラムを作りたい。左から順に小さい因子が並び、同じ因子が複数回現れる場合はそれをべき乗の形で表示させたい。
```bash
> ./primeDecomposition.exe 180
  --> 2^2 3^2 5
> ./primeDecomposition.exe 119
  --> prime number
```

### 整数の演算

### 制御構造

### 素数判定プログラムの作成

### 繰り返し処理

### 関数

### 素因数分解プログラムの作成


