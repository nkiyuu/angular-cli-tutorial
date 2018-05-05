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

```
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