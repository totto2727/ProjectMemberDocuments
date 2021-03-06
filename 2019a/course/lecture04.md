# Apache Wicket-1

## キーワード

* Apache
* Wicket
* Maven
* localhost

## 概要

Apache Wicketは本学のシステム(ポータルやeラーニングシステム)などで使われているコンポーネント指向のフレームワークである。<br/>
後に上級生との共同でのシステム開発に入った際にはこれから紹介する知識が必要となるため事前に学んでおくこと。<br/>

WicketはJavaファイルとhtmlファイルが1対1のセットとなった構造をしており、htmlで `wicket:id` を指定することで、対応するJavaファイルにてコンポーネントをその `wicket:id` に追加することができる

言い換えると、単純なボタンやリンク、フォームをhtmlに記述し、それらが実際に行う処理はJavaファイルで定義するということである。

### 導入

#### プロジェクトの作成

1. Wicketの [QuickStartページ](https://wicket.apache.org/start/quickstart.html) を開く
2. Group IDを `com.prmn` に書き換える
3. Artifact IDを `prmn-wicket` に書き換える
4. `generated command line` 内のコードをコピーする
5. プロジェクトを配置したいディレクトリを開く(大学PCなら"端末から開く"、自前のPCならCドライブ直下、Macなら"ターミナル"を開いて、ホームディレクトリ)
6. 4でコピーしたコマンドをペーストして実行する
7. 指定したディレクトリに `prmn-wicket` ディレクトリが生成されていれば完了

#### IntelliJでImport

1. IntelliJの起動画面(Welcome to IntelliJ IDEA)を開く(既にプロジェクトを開いている場合には"File"->"Close Project")
2. `Import Project` を選択し、作成したプロジェクトを指定する
3. ラジオボタンで `Import project from external model` を選択し、Mavenを選択する
4. `Import Maven projects automatically` にチェックを付けて"next"を押す
5. そのまま"next"を押す
6. SDKを選択(今期のプロジェクトメンバーではAdoptOpenJDKのバージョン11を使用する)し、"next"を押す
7. そのまま"Finish"を押す

#### 起動の確認

1. IntelliJで"prmn-wicket"->"src"->"test"->"java"->"com.prmn"->"Start"を開く
2. mainを実行する
3. `[main] INFO org.eclipse.jetty.server.Server - Started @2505ms`と表示されるのを確認する("2505ms"の部分は環境により変動する)
4. ブラウザで `localhost:8080"` を検索する
5. "Congratulations!"と表示されていれば成功

### Model

#### MVC

Wicketに限らず、多くのGUIフレームワークでは、 "MVC" というデザインパターンが取り入れられている。<br/>
なお、デザインパターンとは長年の経験をかけて、システム開発における定石/パターンが集約されたものであり、例としてはGoF(Gang of Four)のデザインパターンが挙げられる。<br/>
MVCとはModel-View-Controllerの略であり、それぞれがシステムにおける役割に応じて分類されたクラスである。<br/>
その中でもModelというのは、htmlなどの "画面に表示するためのデータおよびそれを間接的に取り扱うオブジェクト" のことを言う。<br/>

#### WicketにおけるModel

WicketではさまざまなModelが用意されており、単純な文字列を表現するLabelや、コレクションの一覧を表示するListViewなどにセットして用いられる。<br/>
なお、WicketのModelは全て `IModel` インターフェイスを実装している。<br/>
Modelにはオブジェクトをセットもしくは取り出すことができ、それぞれコード上では以下のように記述する。

staticな `Model#of(T)` によるモデル化

```java
// HogeDataをモデルに入れる
HogeData hogeData = new HogeData();
IModel<HogeData> hogeModel = Model.of(hogeData);
```

`IModel#getObject()` によるオブジェクトの取り出し

```java
// HogeDataモデルからセットされたオブジェクトを取り出す
hogeModel.getObject();
```

実際には、コンポーネントへはモデルではなく実際のオブジェクトを渡しても動作する場合がほとんどであるが、Modelを使うか実体を使うかの基準は、慣れるまでは *そのオブジェクトが将来変化する可能性があるか* で決めると良い。<br/>
例えば、ButtonやLinkをクリックした場合や、各種Fieldに値を入力した際に変化する可能性のあるオブジェクトはほぼModelが使われる。

### コンポーネント

Wicketにはページ上に表示するための部品(コンポーネントという)が用意されており、これを使用することでページ上にリンクやボタンなどを配置することができる。<br/>
今回、よく使われるコンポーネントとして以下を紹介する。

#### Label

Labelは単純なラベル(表示するための文字列を表す)を生成する。<br/>
用途としては、何かしらのアクションによって表示が切り替わるもの、例えば日時やログインユーザー名前などを表示する場合に使われる。

使用例は以下の通りである。

```java
// wicket:id="exampleLabel"に対して"This is an example."という文字列を設定する
add(new Label("exampleLabel", "This is an example."));
```

#### Button

Buttonはボタンを生成する。<br/>
具体的な動作は Button#onSubmit() を実装することで定義することができる。<br/>
具体的な例として、ボタンが押された場合に標準出力に現在時刻を表示するコードは以下のようになる。

```java
// wicket:id="button"に対してボタンを生成する
add(new Button("button"){
  @Override
  public void onSubmit(){
    System.out.println(LocalDateTime.now().toString());
  }
});
```

#### Link

Linkはリンクを生成する。<br/>
基本的な動作はButtonと同じである。<br/>
ただし、Buttonとは違い、押された場合の動作は Link#onClick() を実装することで定義することができる。<br/>

```java
// wicket:id="link"に対してリンクを生成する
add(new Link<Void>("link"){
  @Override
  public void onClick(){
    System.out.println("Link clicked.");
  }
});
```

#### ListView

ListViewはコレクション(Listなど)の中身をリスト形式で表示する場合に用いられる。<br/>
コレクションの内容をどのように処理するかは、ListView#populateItem() を実装することで定義できる。<br/>
なお、ListView#populateItem()は抽象メソッドとなっており、かならず実装しなければならない。

listItem中のインスタンスを取得する場合には、 `ListItem<>::getModelObject` を用いる。

```java
// Accountのリスト(要素の追加は省略する)
List<Account> accounts = new ArrayList<Account>();

// accountsのモデル化
IModel<List<Account>> accountsModel = Model.ofList(accounts);

ListView<Account> accountListView = new ListView<>("accountListView", accountsModel){
  // populateItemは抽象メソッド
  @Override
  protected void populateItem(ListItem<Account> listItem){
    listItem.add(new Label("accountId", listItem.getModelObject().getId()));
  }
};
```

```html
<div>
  <ul wicket:id="accountListView">
    <li wicket:id="accountId"></li>
  </ul>
</div>
```

## 演習課題

### 課題に取り組む前に

1. HomePage.htmlを開き、\<body>タグ内のすべての要素を削除する
2. 同じくHomePage.html内の、`<link href='https://fonts.googleapis.com/css?family=Yanone+Kaffeesatz:regular,bold' rel='stylesheet' type='text/css'/>` を削除する
3. 同じくHomePage.html内の、`<link rel="stylesheet" href="style.css" type="text/css" media="screen" title="Stylesheet"/>` を削除する
4. \<html>タグを、`<html xmlns:wicket="http://wicket.apache.org">`に変更する
5. HomePage.javaを開き、`add(new Label("version", getApplication().getFrameworkSettings().getVersion()));` を削除する
6. main()を実行してブラウザでlocalhost:8080を開き、何も書かれていないページが表示されていれば完了

### 課題1(練習)

HomePage.htmlとHomePage.javaを編集し、Labelを用いて自分の学籍番号を表示しなさい。

1. HomePage.htmlのbodyタグ内に `<span wicket:id="studentNumber"></span>` を追加する
2. HomePage.javaのコンストラクタ内に以下のコードを追加する

```java
String studentNumber = "b218xxxx";    // xxxxの部分は自分の学籍番号
IModel<String> studentNumberModel = Model.of(studentNumber);      // studentNumberのモデル
Label studentNumberLabel = new Label("studentNumber", studentNumberModel);
add(studentNumberLabel);
```

編集後、Startクラスのmainメソッドを実行し `localhost:8080` で自分の学籍番号が表示されることを確認する。<br/>
以下、特に指示しなくても `localhost:8080` で検索し動作確認を行うこと。

### 課題2

HomePage.htmlとHomePage.javaを編集し、リンクを押すことで現在時刻を表示できるようにしなさい。

ただし、Linkは変数名を `updateCurrentTimeLink`、Labelは `currentTimeLabel`とし、wicket:idも同一のものを指定することとする。

ヒント1. Linkをインスタンス化する際、型引数にはVoidを指定する<br/>
ヒント2. IModel#setObject(T) で表示する文字列をセットし直す<br/>
ヒント3. 現在時刻の取得には LocalDateTime#now() を用いると良い(さらにtoString()を使用して文字列に変換する)

### 課題3

ListViewを用いてコレクションの内容を表示する。

"com.prmn"の直下に"bean"パッケージを作成する。<br/>
作成したbeanパッケージ内に、以下の要件を満たすクラスを作成する。

1. クラス名はReport
2. フィールドとして id(long), authorName(String), title(String), details(String), postedAt(Timestamp)を持つ
3. フィールドは全てprivateでアクセサを持つ
4. コンストラクタにてすべてのフィールドを初期化すること(ただし、postedAtのみLocalDateTime#now()を用いて初期化するものとする)
5. `Serializable`インターフェイスを実装する

HomePage.htmlとHomePage.javaを編集し、Reportの内容をListViewを用いて表示できるようにしなさい。<br/>
なお、ListViewに渡すListには、少なくとも2つ以上の要素を追加しておくこと(初期化する内容は問わない)。

ヒント1. ListViewをインスタンス化する際に、第2引数に `IModel<List<Report>>` を渡す<br/>
ヒント2. ListViewに何かを追加(この課題では表示)したい場合には、ListView#populateItem(ListItem listItem) のlistItemに対してadd()する
