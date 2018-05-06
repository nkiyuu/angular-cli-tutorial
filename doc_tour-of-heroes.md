# tour of heroes のチュートリアル

このチュートリアルを終えるとこんなことができるようになるよ．



- そもそもあるangularnの機能で，要素を隠したり表示したりヒーローのリストを見せたりできる．
- Angular コンポーネントを作成することでヒーローの詳細を表示したり，ヒーローの配列を表示する．
- 閲覧のみのデータの一方向データバインディング
- 双方向データバインディングによるモデルをアップデートできるフィールドの作成
- ヒーローリストからヒーローを選択して選択したヒーローの情報を詳細画面で編集
- パイプを使ってデータを整形する．
- 共通サービスを作ってヒーローを管理する
- ルーティングをすることによりそれぞれの画面やコンポーネントへアクセスできるようにする



## 2章 applicateion shell

app componetnt の中身をちょっといじった

app component のtitle 部分を書いて template に{{ }} で囲むことによってビューの方にその値を出せる

Css もちょっと書いたよ



## 3章 ヒーローエディター

新しいコンポーネントを作ろう

```bash
ng generate component heroes
```

src/app/heroes に新しく HeroesComponent が作成される．

コンポーネント作成時は必ずAngular のコアライブラリにある Compornent とアノテーション@をつけなければならない．

@Component Angularのメタデータだと判別するためのデコレーターの役割をする

CLIは以下の三つのメタデータを作成

1. selector - css で指定するときのセレクターになる
2. templateUrl - テンプレートファイルがどれかを示す
3. styleUrls - css style はどれを用いるかを設定する

Css のセレクターの app-heroes 親のコンポーネント中で使われるときにhtmlタグとして認識される．

ngOnInitはコンポーネントが作成された際に発火する．初期化の時のロジックを書くと良いよ．

常にコンポーネントは`export ` しておくといいよ．

### hero の情報を加える

`heroes.component.ts` に'Wingstorm' と言う名のheroを加える

```hero.component.ts
hero = 'Windstorm'
```

テンプレートファイル`heroes.component.html` に{{ }} を使ってさっきのheroを書く．Angular側が勝手にバインドしてくれるよ．

```
{{ hero }}
```

 最後に，親のコンポーネントの`app.component.html`にheroesコンポーネントのタグ`<app-heroes></app-heroes>`を入れてあげると実際に表示されるよ．

### hero クラスを作成

本物のヒーローには名前以外もあるはず．
`src/app/hero.ts`にヒーロークラスを作成して`id` と`name` を追加する．

```typescript
export class Hero {
  id: number;
  name: string;
}
```

HeroesComponetnクラスでheroクラスをインポートする
heroのプロパティをヒーロークラスに合わせて書き換える

```typescript
import { Component, OnInit } from '@angular/core';
import { Hero } from '../hero';

@Component({
  selector: 'app-heroes',
  templateUrl: './heroes.component.html',
  styleUrls: ['./heroes.component.css']
})
export class HeroesComponent implements OnInit {
  // ここから //
  hero: Hero = {
    id: 1,
    name: 'Windstorm'
  };
  // ここまで //

  constructor() { }

  ngOnInit() {
  }

}
```

string型からhero型にheroを変えてしまったのでブラウザからうまく表示できないので変更する．

### ヒーローオブジェクトを表示する

テンプレートのバインディングの部分を修正してヒーローの名前とidを表示するようにする．

```Html
<h2>{{ hero.name }} Details</h2>
<div><span>id: </span>{{hero.id}}</div>
<div><span>name: </span>{{hero.name}}</div>
```

ブラウザが更新されてヒーローの情報が表示されるよ

### UppercasePipe で見た目を整える

```Html
<h2>{{ hero.name | uppercase }} Details</h2>
```

ブラウザが更新されてヒーローの名前が大文字で表示されるようになった．

パイプ`|` の右側に書いた`uppercase` は組み込みの`UppercasePipe`を有効にするコマンドです．

パイプは文字列や通貨額，日付などの表示を指定するのに有効な方法．angularにはいくつかの組み込みのパイプや，自分で作ることもできる．

### ヒーローを編集する

`<input>`のテキストボックスを利用してヒーローの名前を変更できるようにする．

テキストボックスはヒーローの名前を表示することと，ヒーローの名前をユーザの入力で更新できるようにする．つまり，コンポーネントクラスから画面に向けてデータを表示するのと，画面側からクラスに向けてデータを入力していることになる．

上記を実現するために，`<input>`タグと`hero.name` プロパティの両方を使って双方向データバインディングができるようコードを修正する．

#### 双方向データバインディング

以下のようにコードを書き換える

```html
<div>
    <label>name:
      <input [(ngModel)]="hero.name" placeholder="name">
    </label>
</div>
```

[(ngModel)]はangularの双方向データバインディングのための構文です．

`hero.name` プロパティからテキストボックスへ，テキストボックスから`hero.name` プロパティへのデータの双方向の対応を取るため，HTMLのテキストボックスに`hero.name`プロパティが[(ngModel)]によってバインドされます．

#### FormsModule を追加する

[(ngModel)]を追加するとアプリがうまく動かなくなります．

デベロッパーツールを開くと以下のようなエラーが表示されています．

```Javascript
Template parse errors:
Can't bind to 'ngModel' since it isn't a known property of 'input'.
```

`ngModel`は確かにangular で使えるプロパティですが，デフォルトで使うことはできません．
`ngModel`はオプショナルの`FormsModule` で定義されているので，インポートしなければなりません．

#### AppModule

複数のソースコードをどのように組み合わせるのかや，アプリケーションに必要なファイルやライブラリは何なのかを記述する必要があります．この情報をメタデータといいます．

今までの中でコンポーネントクラスに記述した`@Component`デコレータはメタデータの一例です．この他に覚えておきたいメタデータは**@ngModule**デコレータです．
もっとも重要な@ngModuleデコレータは**AppModule** クラスに記述されています．

Angular CLI でプロジェクトを作成する際に，`src/app/app.module.ts` に`AppModule` クラスが生成されます．ここに今回使う`FomrsModule`をインポートします．

#### FormsModule のインポート

 `src/app/app.module.ts` に以下を追記します．

```typescript
import { FormsModule } from '@angular/forms'; // <-- NgModelはここで実装されています
```

`FormsModule` を`@NgModule`の中の`imports`に追記します．この`imports`にはアプリに必要な外部ライブラリの一覧をリストの形で記述します．

```typescript
imports: [
  BrowserModule,
  FormsModule
],
```

上記を記述すると，ブラウザが更新されてアプリが動くようになったと思います．また，テキストボックスにヒーローの名前が表示されて，テキストボックスの中身を変更するとヒーローの名前も変更されるようになったと思います．

#### HeroesComponent の宣言

全てのコンポーネントはNgModuleで宣言される必要があります．
ところが，`HeroesComponent` に関しては，宣言をどこにも書いていませんでした．しかし，アプリケーションは正しく動いています．

これは，Angular CLI でコンポーネントを作成すると，自動的に`HeroesComponent`を`AppModule`内に宣言してくれるためです．

実際に`src/app/app.odule.ts` の中身を確認してみると，

```typescript
import { HeroesComponent } from './heroes/heroes.component';
```

```typescript
declarations: [
  AppComponent,
  HeroesComponent
],
```

上記のように，`HeroesComponent`が`AppModule`ないで宣言されていることが確認できます．



## ４章　Heroリストの表示

この章ではヒーローたちのリストを表示したり，選択されたヒーローの詳細を表示的たりするようにします．

### ヒーローのモックの作成

表示するヒーローたちの一覧を作成する．

実際には，ヒーローのデータはサーバーから取得することになるが，現状その環境がないのでモックのヒーロー一覧を作成して擬似的にサーバーから取得したこととします．

`src/app/mock-heroes.ts`に以下のモックのデータを作成します．

``` Typescript
import { Hero } from './hero';

export const HEROES: Hero[] = [
  { id: 11, name: 'Mr. Nice' },
  { id: 12, name: 'Narco' },
  { id: 13, name: 'Bombasto' },
  { id: 14, name: 'Celeritas' },
  { id: 15, name: 'Magneta' },
  { id: 16, name: 'RubberMan' },
  { id: 17, name: 'Dynama' },
  { id: 18, name: 'Dr IQ' },
  { id: 19, name: 'Magma' },
  { id: 20, name: 'Tornado' }
];
```

### ヒーロー一覧の表示

では，`HeroesComponent` の一番上部にヒーローの一覧を表示してみましょう．

`src/app/heroes/heroes.component.ts` の`HeroesComponent`クラスでモックの`Heroes`をインポートします．

```Typescript
import { HEROES } from '../mock-heroes';
```

インポートした`HEROES`を`heroes`プロパティとして利用します．

```Typescript
heroes = HEROES;
```

### *ngFor を用いてリストのヒーロー一覧を表示する

テンプレートファイル`src/app/heroes/heroes.component.html`に以下の変更を加えます．

- `<h2>`要素を1行めに追加
- 2行目に`<ul>`要素を追加
- `<ul></ul>`の中に`<li>`要素を追加．この部分で`hero`の中身を表示します．
- 見た目の調整のためにクラスを追加します．

実際のコードはこんな感じ　

```html
<h2>My Heroes</h2>
<ul class="heroes">
  <li>
    <span class="badge">{{hero.id}}</span> {{hero.name}}
  </li>
</ul>
```

そして`<li>`要素を以下のように書き換えます

```Html
<li *ngFor="let hero of heroes">
```

`*ngFor`はAngularの繰り返しのコマンドです．リスト中にあるそれぞれの要素にたいしてホストの要素を繰り返します．

実際のコードを例にとると以下のようになります．

- `<li>`要素がホストの要素
- `heroes`が`HeroesComponent`クラスから得られたリスト
- `hero`はリスト中のそれぞれのイテレーションのヒーローオブジェクトを表します．

ブラウザが更新され，ヒーローのリストが表示されます．

### ヒーロー一覧の見た目を整える

現状の見た目はちょっとわかりにくいので見た目を整えて綺麗にした方が良さそうでしょう．また，カーソルを合わせたり選択したりした時に，見た目にわかりやすくしてあげると良さそうです．

チュートリアルの最初で，基礎となるスタイルを`style.css` に記述しました．しかしながら今回作成したヒーローリストに関して，`style.css`では何も記述していません．

そこで，`style.css`に追記することで見た目をリッチにすることは可能です．

ここで，特定のコンポーネント中でのみスタイルを定義し，コンポーネント中でhtmlやcssを完結させることが推奨されています．

これによって，コンポーネントの再利用が容易になりったり，グローバルのスタイルが変わったとしてもコンポーネント中のスタイルは定義したままの見た目で表示することが可能です．

コンポーネントのプライベートなスタイルは`@Component.styles`中に記述するか，`@Component.styleUrls`で指定したファイルに記述することによって定義できます．

CLIを使ってコンポーネントを作成した際に空の`heroes.component.css`が`HeroesComponet`中に作成され`@Component.styleUrls`に記述されます．

```Typescript
@Component({
  selector: 'app-heroes',
  templateUrl: './heroes.component.html',
  styleUrls: ['./heroes.component.css']
})
```

この章の最後にstyleの一例を載せたので`heroes.component.css`に記述してみてください．スタイルがヒーローリストに対して適用されるはずです．

### Master/Detail

ユーザがリスト中のヒーロを選択した際，リストの下に選択したヒーローの詳細を表示させます．

この節ではクリックイベントの検知とヒーローの詳細の更新を扱います．

#### クリックイベントの追加

`<li>`に対してクリックイベントを設定します．以下のように`<li>`に追記します．

```html
<li *ngFor="let hero of heroes" (click)="onSelect(hero)">
```

`(click)`を設定することによって`<li>`はクリックのイベントを検知できるようになります．この`<li>` がクリックされたとき，angularは`onSelect(hero)`を実行します．

 このあと`onSelect()`の実際の動作を`HeroComponent`に記述します．クリックされたヒーローオブジェクトに対して`onSelect`関数を適用します．

####  クリックされた時の動作の定義

`hero`変数の名称を`selectedHero`に変更しましょう．このとき，初期値は何も設定しないでください．アプリケーションの起動時，まだどのヒーローも選択されてはいないからです．

`src/app/heroed/heroes.component.ts`中の`onSelect()`の中身を実装していきましょう．この関数では，クリックされたヒーローオブジェクトを`selectedHero`に代入します．

```Typescript
selectedHero: Hero;

onSelect(hero: Hero): void {
  this.selectedHero = hero;
}
```

#### ヒーローの詳細表示を変更する

テンプレート側ではまだ上記の変更を加える前の`hero`変数に対してのテンプレートでした．そこで`hero`を`selectedHero` に変えるなどの修正を行います．`heroes.component.html`を以下のように変更しましょう．

```html
<h2>{{ selectedHero.name | uppercase }} Details</h2>
<div><span>id: </span>{{selectedHero.id}}</div>
<div>
  <label>name:
    <input [(ngModel)]="selectedHero.name" placeholder="name">
  </label>
</div>
```

#### *ngIfを使ってヒーローが選択された時だけ詳細を表示

ブラウザが更新されると，アプリがうまく動かなくなっていると思います．

ブラウザのデベロッパーツールを開くとコンソールに以下のようなエラーメッセージが表示されています．

```Javascript
HeroesComponent.html:3 ERROR TypeError: Cannot read property 'name' of undefined
```

リスト中のヒーローのうち誰か一人を選択してみてください．アプリは正常に動き出すと思います．

##### 何が起きたのか

アプリケーションの起動時，`selectedHero`は`undefined`になるように記述しています．

angularはテンプレートの中にある`selectedHero.name`を表示しようとしますが，まだ定義されていないため表示することができずエラーが起きてしましいます．

##### なので修正をしましょう

コンポーネントでは`selectedHero`が存在する時のみヒーローの詳細を表示するべきです．

`src/app/heroes/heroes.component.html`のヒーロー詳細を表示する部分を`<div>`で囲み，その`div`に`*ngIf="selectedHero"`を設定しましょう．以下のようになります．

```html
<div *ngIf="selectedHero">

  <h2>{{ selectedHero.name | uppercase }} Details</h2>
  <div><span>id: </span>{{selectedHero.id}}</div>
  <div>
    <label>name:
      <input [(ngModel)]="selectedHero.name" placeholder="name">
    </label>
  </div>

</div>
```

ブラウザの更新後，ヒーローリストが表示され，ヒーローを選択すると選択されたヒーローの詳細が下側に表示されるようになります．

##### なぜ動くようになったのか

`selectedHero`が`undefined`のとき，ヒーロー詳細を表示するDOMは`ngIf`によってHTMLから取り除かれます．これによって未定義の`selectedHero`を表示しようとする処理が行われる心配は無くなります．ユーザがヒーローを選択すると`selectedHero`が値を持ち，`ngIf`によってヒーロー詳細を表示するDOMが追加されます．

#### 選択されたヒーローの見た目をわかりやすくする

ヒーローのリストのすべての要素が同じような見た目をしていると選択されたヒーローが誰なのかわかりにくいです．

ユーザーがヒーローを選択したとき，背景の色を変えることによって選択されたヒーローが誰なのかわかりやすくしましょう．

選択したヒーローの色の変更は`.selected`でさっき定義していました．そこで`<li>`をクリックした時に`.selected`クラスが付与されるように設定しましょう．

Angularでは簡単に状態応じてクラスの付与と除去ができるようになっています．`[class.some-css-class]="some-condition"`と書くとクラスの付与と除去ができます．

`src/app/heroes/heroes.component.html`の`<li>`に以下を記述しましょう．

```html
[class.selected]="hero === selectedHero"
```

現在みている`hero`と`selectedHero`が同じとき`selected`クラスが付与され，異なる`hero`が選択されると除去されます．

以下のようなコードになります．

```html
<li *ngFor="let hero of heroes"
  [class.selected]="hero === selectedHero"
  (click)="onSelect(hero)">
  <span class="badge">{{hero.id}}</span> {{hero.name}}
</li>
```

`src/app/heroes/heroes.component.css`は以下のようになっています．

```Css
/* HeroesComponent's private CSS styles */
.selected {
  background-color: #CFD8DC !important;
  color: white;
}
.heroes {
  margin: 0 0 2em 0;
  list-style-type: none;
  padding: 0;
  width: 15em;
}
.heroes li {
  cursor: pointer;
  position: relative;
  left: 0;
  background-color: #EEE;
  margin: .5em;
  padding: .3em 0;
  height: 1.6em;
  border-radius: 4px;
}
.heroes li.selected:hover {
  background-color: #BBD8DC !important;
  color: white;
}
.heroes li:hover {
  color: #607D8B;
  background-color: #DDD;
  left: .1em;
}
.heroes .text {
  position: relative;
  top: -3px;
}
.heroes .badge {
  display: inline-block;
  font-size: small;
  color: white;
  padding: 0.8em 0.7em 0 0.7em;
  background-color: #607D8B;
  line-height: 1em;
  position: relative;
  left: -1px;
  top: -4px;
  height: 1.8em;
  margin-right: .8em;
  border-radius: 4px 0 0 4px;
}
```





f