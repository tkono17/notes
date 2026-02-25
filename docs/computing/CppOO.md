# オブジェクト指向

## 導入

オブジェクト指向はC++の主要な機能で、大きなプログラムを整理して開発していく上で非常に強力なプログラミング手法である。

## 目標

ここでは、少し複雑なデータを扱うときに役立つプログラミングの機能・手法として、データ構造、クラス、オブジェクト指向について学ぶ。
予定表を題材に、

* 複雑なデータをプログラムの中でどう表現するか
* クラスの機能と使い方
* クラスの継承

がプログラミングの中でどのように利用されるかを見ていく。
具体的には予定表が記述されたCSV形式のテキスト・ファイルを読み込んで、それをきれいに整形して表示するプログラムを作っていく。

## 予定データ

カレンダに予定を書き込む上で必要なデータはどんなものか。ここでは、最低限の情報として以下に示すデータを用いる。

* 予定の名前
* 日付と曜日 (例: 2026/2/25, 水)
* 開始時間 (例: 9:00)
* 終了時間 (例: 10:30)
* 場所 (例: 理1-201)
* タイプ (例: 授業, アルバイト, サークル, etc.)

C++のデータ型としては

| データの用途 | 変数名 | 型 | 説明 |
|-------------|-------|----|------|
| 予定の名前 | name | std::string | 日本語可 |
| 日付 | date | std::string | 2025-02-25の形式の文字列 |
| 曜日 | dayOfWeek | int | 日->0, 月->1, ..., 土->6 と整数で表現 |
| 開始時間 | startTime | std::string | 09:00の形式の文字列 |
| 終了時間 | endTime | std::string | 15:05 の形式の文字列 |
| 場所 | name | std::string | 日本語可 |
| タイプ | eventType | std::string | 授業、アルバイト、サークル、研究、イベントのいずれか |

このデータをテキスト・ファイルに書いて保存しておきたい。しかも、あとでプログラムがそのファイルを読んで内容を理解する必要があるので、明確に定義された形式で記述する必要がある。ここでは、CSV形式を採用することにする。CSVはcomma-separated valuesの略で、一つ一つのデータをカンマで区切って書いたものである。一つの予定を書き終わったところで改行して、1行に1つの予定を書いていく。

```csv title="event_data.csv"
物理学実験,2026-04-13,1,13:20,16:30,共3-508,授業
高エネルギー物理学特論,2026-04-14,2,13:20,14:50,理1-221,授業
高エネルギー物理学演習,2026-04-17,5,10:40,12:10,理1-221,授業
研究室ミーティング,2026-04-17,5,13:00,15:00,理1-221,研究
大学院オープンキャンパス,2026-04-18,6,11:00,14:00,理1-221,イベント
```
event_data.csvというファイルに上の内容を書いて保存しておく。このファイルに書く内容は、自分の好きなように予定を書いてよい。ただし、1行にカンマで区切られたデータが7個なければならない。

## 構造体と公開クラス

予定表を作るデータは、複数の予定からなるのでC言語の基本的なデータ型と配列を用いると、以下のようなデータを使って必要なデータを扱うことができる。ただし、

```c++ title="予定表のデータ構造" linenums="1"
const unsigned int MaxEvents = 1000;
unsigned int numberOfEvents = 0;

std::string names[MaxEvents];
std::string dates[MaxEvents];
int dayOfWeeks[MaxEvents];
std::string startTimes[MaxEvents];
std::string endTimes[MaxEvents];
std::string locations[MaxEvents];
std::string eventTypes[MaxEvents];
```

配列を使う際には、予め配列の長さを決めておかないといけないので、予定表に書くだろう予定の数の最大値を推定して十分大きな長さの配列を確保しておく必要がある。この例では最大1000個としている。

一つ前の節で作成したevent_data.csvを開いて、ここで定義した配列にデータを読み込むプログラムを作成する。プログラムの第1引数でCSVファイル名を受け取ることにする。

```c++ title="readEventArrays.cxx" linenums="1"
#include <iostream>
#include <fstream>
#include <string>
#include <cstdlib>

int main(int argc, char* argv[]) {
  std::string csvFile = "";
  if (argc == 2) {
    csvFile = argv[1];
  } else {
    std::cout << "Usage: " << argv[0] << " <csvFileName" << std::endl;
    exit(-1);
  }

  const unsigned int MaxEvents = 1000;
  
  std::string names[MaxEvents];
  std::string dates[MaxEvents];
  int dayOfWeeks[MaxEvents];
  std::string startTimes[MaxEvents];
  std::string endTimes[MaxEvents];
  std::string locations[MaxEvents];
  std::string eventTypes[MaxEvents];

  unsigned int nevents = 0;

  std::ifstream fin(csvFile.c_str());
  char line[1000];
  while (fin.good() && !fin.end()) {
    fin.getline(line, 1000);
    std::string sline(line);
    if (std::getline(sline, word, ',')) names[nevents] = word;
    if (std::getline(sline, word, ',')) dates[nevents] = word;
    if (std::getline(sline, word, ',')) dayOfWeeks[nevents] = std::atoi(word);
    if (std::getline(sline, word, ',')) startTimes[nevents] = word;
    if (std::getline(sline, word, ',')) endTimes[nevents] = word;
    if (std::getline(sline, word, ',')) locations[nevents] = word;
    if (std::getline(sline, word, ',')) eventTypes[nevents] = word;

    if (names.size() != eventTypes.size()) {
        std::cout << "Row " << nevents 
          << ": Event data has less than 7 columns" << std::endl;
        exit(-2);
    }
    nevents ++;
  }

  std::cout << "Number of events read: " << nevents << std::endl;
  for (unsigned int ievent=0; ievent<nevents; ++ievent) {
    std::cout << "Event " << ievent << std::endl;
    std::cout << "  name       : " << names[ievent] << std::endl;
    std::cout << "  date       : " << dates[ievent] << std::endl;
    std::cout << "  day of week: " << dayOfWeeks[ievent] << std::endl;
    std::cout << "  start time : " << startTimes[ievent] << std::endl;
    std::cout << "  end time   : " << endTimes[ievent] << std::endl;
    std::cout << "  location   : " << locations[ievent] << std::endl;
    std::cout << "  event type : " << eventTypes[ievent] << std::endl;
  }
  return 0;
}
```

予定に関する属性7個がそれぞれ別々の配列に保存されて、ある予定に関するデータにアクセスするには配列の要素番号をそろえればよい。

「予定の開始時刻」を「2番目の予定」について知りたいときには、```startTimes[2]```としてデータにアクセスすることになる。
このようにしてプログラムを書くことは可能だが、直感的には、

「2番目の予定」の「開始時間」を知りたい、というような考え方をすることが多いでしょう。このような使い方をするには、一つの予定に関するデータをまとめたデータを用意して、その配列を使えればよい。C/C++言語にある構造体はまさにそのようなもので、複数の関連するデータをまとめて、新しい型を定義するものである。

```c++ title="Event構造体" linenums="1"
struct Event {
  std::string name;
  std::string date;
  int dayOfWeek;
  std::string startTime;
  std::string endTime;
  std::string location;
  std::string eventType;
};
```
構造体 (structure)はC言語でも利用できる機能で、struct Eventというのは、Event構造体という新しい型を宣言するものである。C言語では「struct Event」が型名になるのに対して、C++言語ではEventが型名になるという違いがあったりで少し紛らわしい。

C++言語では構造体をさらに発展させてクラスというものが導入された。C++でもstructを利用可能だが、これをクラス宣言とpublicアクセス指定子を使って宣言することもできる。

```c++ title="Eventクラス" linenums="1"
class Event {
public:
  std::string name;
  std::string date;
  int dayOfWeek;
  std::string startTime;
  std::string endTime;
  std::string location;
  std::string eventType;
};
```

C++では、上のstructによる宣言でもclassとpublicを使った宣言は同等で、新しい型としてEvent型というものを使うことができる。
```c++
Event event;
```
のようにして、Event型のデータを指す変数eventを定義できる。Event型のデータのことを、Eventオブジェクトと呼ぶ。クラスがデータの型を指すのに対して、オブジェクトというのはクラスで宣言されたデータを保持するデータのことを指す。
そして、eventオブジェクトの名前や開始時間には
```c++
event.name = "Event name";
event.startTime = "12:00";
```
のように「変数名.属性名」という形でアクセス可能である。
これを使ってCSVファイルからデータを読み込むプログラムを作ると次のようになる。

```c++ title="readEventData.cxx" linenums="1"
#include <iostream>
#include <fstream>
#include <string>
#include <cstdlib>

class Event {
public:
  std::string name;
  std::string date;
  int dayOfWeek;
  std::string startTime;
  std::string endTime;
  std::string location;
  std::string eventType;
};

int main(int argc, char* argv[]) {
  std::string csvFile = "";
  if (argc == 2) {
    csvFile = argv[1];
  } else {
    std::cout << "Usage: " << argv[0] << " <csvFileName" << std::endl;
    exit(-1);
  }
  
  const unsigned int MaxEvents = 1000;
  Event events[MaxEvents];
  unsigned int nevents = 0;

  std::ifstream fin(csvFile.c_str());
  char line[1000];
  while (fin.good() && !fin.end()) {
    fin.getline(line, 1000);
    std::string sline(line);

    Event event;
    if (std::getline(sline, word, ',')) event.name = word;
    if (std::getline(sline, word, ',')) event.date = word;
    if (std::getline(sline, word, ',')) event.dayOfWeek = std::atoi(word);
    if (std::getline(sline, word, ',')) event.startTime = word;
    if (std::getline(sline, word, ',')) event.endTime = word;
    if (std::getline(sline, word, ',')) event.location = word;
    if (std::getline(sline, word, ',')) event.eventType = word;

    if (event.eventType == "") {
        std::cout << "Row " << nevents 
          << ": Event data has less than 7 columns" << std::endl;
        exit(-2);
    }
    events[nevents] = event;
    nevents ++;
  }

  std::cout << "Number of events read: " << nevents << std::endl;
  for (unsigned int ievent=0; ievent<nevents; ++ievent) {
    std::cout << "Event " << ievent << std::endl;
    std::cout << "  name       : " << events[ievent].name << std::endl;
    std::cout << "  date       : " << events[ievent].date << std::endl;
    std::cout << "  day of week: " << events[ievent].dayOfWeek << std::endl;
    std::cout << "  start time : " << events[ievent].startTime << std::endl;
    std::cout << "  end time   : " << events[ievent].endTime << std::endl;
    std::cout << "  location   : " << events[ievent].location << std::endl;
    std::cout << "  event type : " << events[ievent].eventType << std::endl;
  }
  return 0;
}
```

このコードを見ると、次のようにしてEvent型のデータ1000個からなる配列eventsを作っている。
```c++
const unsigned int MaxEvents = 1000;
Event events[MaxEvents];
```
これとは別にwhileループの中で```Event event;```と、一時的な変数を用意して、1行読み込んだときのデータをこの中に保存しておいて、```events[ievent] = event```のようにして、データがまとまったところでこれを予定表全体のデータを保持するevents配列に追加している。

最後にeventsデータを出力する部分を見ると、
```c++
events[ievent].name
```
のようにして、「ievent番目の予定」の「名前」属性にアクセスしている。

## データの隠蔽とメンバー関数

上のプログラムを関数を使って整理してみる。以下の処理を実行している部分のコードを関数として切り離して、main関数を読みやすくしてみる。

* 1行分のデータからEvent型データを作る
* Eventの配列を読みやすい形で出力する

```c++ title="printEventData.cxx" linenums="1"
#include <iostream>
#include <fstream>
#include <string>
#include <cstdlib>

class Event {
public:
  std::string name;
  std::string date;
  int dayOfWeek;
  std::string startTime;
  std::string endTime;
  std::string location;
  std::string eventType;
};

int fillEvent(Event& event, const std::string& line) {
  std::string word = "";
  if (std::getline(line, word, ',')) event.name = word;
  if (std::getline(line, word, ',')) event.date = word;
  if (std::getline(line, word, ',')) event.dayOfWeek = std::atoi(word);
  if (std::getline(line, word, ',')) event.startTime = word;
  if (std::getline(line, word, ',')) event.endTime = word;
  if (std::getline(line, word, ',')) event.location = word;
  if (std::getline(line, word, ',')) event.eventType = word;
  
  if (event.eventType == "") {
    std::cout << "Row " << nevents 
          << ": Event data has less than 7 columns" << std::endl;
    return -1;
  }
  return 0;
}

void printEvent(const Event& event) {
  std::cout << "Event" << std::endl;
  std::cout << "  name       : " << event.name << std::endl;
  std::cout << "  date       : " << event.date << std::endl;
  std::cout << "  day of week: " << event.dayOfWeek << std::endl;
  std::cout << "  start time : " << event.startTime << std::endl;
  std::cout << "  end time   : " << event.endTime << std::endl;
  std::cout << "  location   : " << event.location << std::endl;
  std::cout << "  event type : " << event.eventType << std::endl;
}

int main(int argc, char* argv[]) {
  std::string csvFile = "";
  if (argc == 2) {
    csvFile = argv[1];
  } else {
    std::cout << "Usage: " << argv[0] << " <csvFileName" << std::endl;
    exit(-1);
  }
  
  const unsigned int MaxEvents = 1000;
  Event events[MaxEvents];
  unsigned int nevents = 0;

  std::ifstream fin(csvFile.c_str());
  char line[1000];
  while (fin.good() && !fin.end()) {
    fin.getline(line, 1000);
    Event event;
    if (fillEvent(event, line) == 0) {
      events[nevents] = event;
    }
    nevents ++;
  }
  std::cout << "Number of events read: " << nevents << std::endl;
　for (int ievent=0; ievent<nevents; ++ievent) {
    printEvent(events[ievent]);
  }
  return 0;
}
```

Eventデータをセットしたり、出力したりする部分を関数として分離することでmain関数の実質的な部分（コマンドライン引数を読み込むところなどを除く）は20行程度になり、このプログラムの構成として以下を実行していることが明確になっただろう。

* Event型の配列の準備
* CSVファイルを開いて1行ずつEventデータを読み込んで配列に追加
* Eventデータを1つずつ出力する

このプログラムをよく見ると、Event型に対して行っていることは、次の2つの処理であることが分かる。

* 1行分のデータから各属性をセットする
* Event型を読みやすい形のテキストで出力する

ここまでは、クラスをstructと同じようにデータ構造として用いているだけであった。クラスでは、データ構造としてだけではなくクラスの中のデータをどのように扱うことができるかを追加で定義することができる。クラスは、データだけでなく、関数もメンバーとして持つことができる。それぞれメンバー・データ（またはデータ・メンバー）、メンバー関数と呼ぶ。

上の2つの機能 (fill, print)をクラスの中で宣言すると、以下のようなクラス宣言になる。関数をクラスの中で定義したことで、関数名も少し変更した。
これらの関数を```public```で追加したのに合わせて、クラスの中のデータ・メンバーのアクセス指定子を```private```とした。アクセス指定子がprivateやprotectedになっているクラスのメンバーはクラスの外に書かれたコードからはアクセス不能になる。

```Event()```と```~Event()```はコンストラクタ、デストラクタと呼ばれる特別な関数で、それぞれオブジェクトを作成する時、破棄する時に自動的に呼び出される関数である。コンストラクタでは、メンバー・データの初期化するのが主な仕事である。デストラクタはオブジェクトを破棄する際に不要になったメモリを解放するコードを書いたりするが、これについてはC++におけるメモリの扱いを説明するところでもう一度考える。

```c++ title="Event.hxx" linenums="1"
class Event {
public:
  Event();
  ~Event() = default;

  int fill(const std::string& line);
  void print() const;
  
private:
  std::string name;
  std::string date;
  int dayOfWeek;
  std::string startTime;
  std::string endTime;
  std::string location;
  std::string eventType;
};
```

このようにクラスのメンバー・データを使う処理をメンバー関数として用意しておくと、上のコードではクラスの外に定義していた```fillEvent```や```printEvent```関数は不要になり、
```
event.fill(line);
event.print();
```
のようにしてEvent型のオブジェクトeventのメンバー関数を呼び出すことで置き換えられる。

## 宣言と実装の分離

前節で書いたクラス宣言からは、Eventクラスにどのようなメンバー・データとメンバー関数があるかは分かるが、```fill()```や```print()```関数が実際にどのような処理を行うかは書かれていない。関数の宣言と実装の分離と同様に、クラスのメンバー関数の実装も宣言とは分けて書くことが通常である。

```c++ title="Event.cxx" linenums="1"
#include "Event.hxx"

Event::Event() {}

int Event::fill(const std::string& line) {
  std::string word = "";
  bool ok = true;

  for (int i=0; i<7; ++i) {
    if (std::getline(line, word, ',')) {
      switch (i) {
        case 0: name = word;
        case 1: date = word;
        case 2: dayOfWeek = std::atoi(word);
        case 3: startTime = word;
        case 4: endTime = word;
        case 5: location = word;
        case 6: eventType = word;
        default:
          std::cout << "Too many columns (>7)" << std::endl;
      }
    } else {
      ok = false;
      break;
    }
  }
  if (!ok) {
    return -1;
  }
  return 0;
}

void Event::print() {
  std::cout << "Event" << std::endl;
  std::cout << "  name       : " << this->name << std::endl;
  std::cout << "  date       : " << this->date << std::endl;
  std::cout << "  day of week: " << this->dayOfWeek << std::endl;
  std::cout << "  start time : " << this->startTime << std::endl;
  std::cout << "  end time   : " << this->endTime << std::endl;
  std::cout << "  location   : " << this->location << std::endl;
  std::cout << "  event type : " << this->eventType << std::endl;
}
```

## クラスの継承

## インタフェースとクラス

