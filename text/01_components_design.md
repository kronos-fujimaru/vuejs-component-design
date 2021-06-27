# Vue.jsで考えるコンポーネント設計

## 0 はじめに

Vue.jsなどのコンポーネントを用いたフロントエンド開発においてコンポーネント設計は必要不可欠です。コンポーネント設計とは、コンポーネントをどのような粒度で、どのような状態で分割してページを構成していくのかを設計することです。

適切にコンポーネント設計をせずに開発を進めると以下のような弊害が生じる可能性があります。

- 再利用性、拡張性の低いコンポーネントの構成になる
- 1つのコンポーネントのコードが肥大化、複雑化する
- componentsディレクトリ配下に粒度、役割の異なるコンポーネントが混在し、管理が複雑化する
- 密結合となりテストがしにくくなる

などなど。

これらの課題を解消するコンポーネントの設計およびプロジェクトのディレクトリ構成について考えていきます。

<br>

**対象読者**

- Vue.jsやReact.jsなどのフロントエンドフレームワークの知識を有する方、およびこれらを用いた開発に携わっている方
- 再利用性、拡張性の高いコンポーネントを作りたいねんと思っている方
- でもコンポーネントの設計に迷走しているねんという方

<br>

## 1 フロントエンド開発におけるコンポーネント設計手法

まずはフロントエンド開発の現場で採用されている設計手法を見ていきます。主な設計手法として以下の2つがあります。

1. Presentational and Container Components パターン
2. Atomic Design パターン

<br>

### 1.1 Presentational and Container Components パターン

<a href="https://medium.com/@dan_abramov/smart-and-dumb-components-7ca2f9a7c7d0">Presentational and Container Components</a> パターンは、Facebook社ReactチームのソフトウェアエンジニアであるDan Abramov氏が考案したコンポーネント設計手法です。コンポーネントを役割毎に **Presentational Component** と **Container Component** の2つに分けてUIを構成します。Reactの設計パターンとして考案されたものですが、Vue.jsに適用すると以下のように解釈できます。

<br>

####Presentational Component

- 見た目に関する責務を負う。
- 子要素としてPresentational ComponentやContainer Componentを持つことができる。
- マークアップやスタイルを書く。
- propsで受け取った値の表示はするが、データを自身で勝手に読み込んだり、改変したりしない。
- 状態を持つことはない（持ったとしても自身のUIに関する状態のみ）。
- 自分以外（StoreやVue Routerなどの機能）に依存しない。

####Container Component

- コンポーネントの振る舞い（ロジック）に関する責務を負う。
- 子要素としてPresentational ComponentやContainer Componentを持つことができる。
- マークアップやスタイルは原則書かない。
- データおよびデータを扱うための関数をPresentational Componentに渡す。
- Storeのデータへのアクセスやミューテーションの呼び出し（commit）、アクションの呼び出し（dispatch）を行う。

<br>

画面描画を担当するPresentational Componentは、propsで受け取った値を表示することを担当します。methodsオプションを持たず、入力やクリックなどのイベントが起こった場合は、$emitを使って上位のコンポーネント（Container Component）に処理を委ねます。

処理を担当するContainer Componentは、Storeへアクセスすることができ、読み込んだデータや関数をpropsでPresentational Componentに渡します。

> Vue.jsでは、親から子へデータを渡すのにpropsを、子から親へ処理を依頼するのに$emitを使用します。Container Componentで用意したデータをprops経由でPresentational Componentに渡し、Presentational Componentで入力などのイベントが発生したら$emitで上位のContainer Componentに処理を依頼するといった流れになります。

<img src="../img/01-01.png">

<br>

#### 利点

- UI部分と振る舞い部分を明確に分離できる。
- 振る舞いに依存しないため再利用性が高くなる。
- デザイナーが振る舞い部分（ロジック）に干渉することなく修正できるため分業しやすくなる。

#### 課題

見た目と振る舞いの2種類では分割の粒度が粗く、ユニットテストがしづらくなる可能性があります。また、プロジェクトのディレクトリ構成においても粒度の異なるコンポーネントが同一ディレクトリに配置され、コンポーネントファイルを探しづらくなるかもしれません。

<br><br>

### 1.2 Atomic Design パターン

<a href="https://atomicdesign.bradfrost.com/">Atomic Design</a> パターンは、アメリカのWebデザイナーであるBrad Frost氏が考案したデザインシステムです。あくまでもデザインの設計思想であり、フロントエンドの実装向けに考案されたものではありません。フロントエンド開発におけるAtomic Designでは、画面要素を5段階に分け、これらの要素を組み合わせることでUIを構成していきます。画面要素の5段階を「原子」や「分子」といった化学用語で表現し、それぞれコンポーネントの粒度を表します。

<img src="../img/01-02.png" width="700">

<br><br>

#### 1) Atom

<table>
<tr>
<td>
最も小さい粒度であり、それ以上分解できない要素です。デザインシステムで最も基本的なデザインパーツです。リンクやテキストボックス、ボタンなどのUIパーツが該当します。
</td>
<td>
<img src="../img/01-02.png" width="800">
</td>
</tr>
</table>
