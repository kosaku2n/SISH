SISH (SImount Shell)
=================
Unix like Shell for OpenQM and UniVerse

[TOC]

## 概要

Unix系のShellライクな操作でコマンド入力が出来るようになります。 
また、アイテムやBASICソースを効率よく編集するスクリーンエディタも備えています。

![](./SISH Manual_files/Shell.png)

## コンパイル確認プラットフォーム

-	OpenQM 3.4-6 or later
-	UniVerse 11.2 or later

## セットアップ

#### ターミナルエミュレータ（クライアント環境）

`SISH`はxterm系の制御シーケンスが理解できるターミナルエミュレータから接続して使用される必要があります。 
Windowsのコマンドプロンプトでは非対応となります。
デフォルトの設定では`Ctrl` `Shift` `Alt`などの装飾キーの多重押しに対応するために[xterm制御シーケンス](http://invisible-island.net/xterm/ctlseqs/ctlseqs.html)のmodifyCursorKeys=2,modifyOtherKeys=2のシーケンスが飛んで来ることが想定されています。

動作確認環境として以下サイトで配布されているGDI版puttyで正しく動作する事を確認しています。

iceiv + putty (GDI版putty) 取得先URL 
[http://ice.hotmint.com/putty/](http://ice.hotmint.com/putty/)

putty.iniにxtermキーシーケンスの設定を追記します。
同梱の[putty.ini](./putty.ini)をputty.exeと同階層に配置すればそのまま利用する事ができます。

また、他にも以下の端末である程度動作する事を確認しています。

- Gnome-terminal(Linux)
- iTurm2(Mac)
	- プロファイルのKeysの設定を`xterm Defaults`に設定
	- プロファイルのColorsの設定を`Solarized light`に設定

これらの端末で正しく動作させる為には、同梱されている[SISH.SETTING.H](./SISH.SETTING.H)のキーシーケンスの設定とカラーパレット設定を端末側とうまく合わせる事により対応させる事ができます。

#### SISHのコンパイルとグローバルカタログ化

同梱のソース`SISH` `SISH.SETTING.H`を`BP`ディレクトリに置きます。 
`BP`ディレクトリのファイルエンコーディングはUTF8に設定して置く必要があります。

以下のコマンドでどのアカウントからも`*SISH`というコマンドで起動できます。

    >BASIC BP SISH
    >CATALOG BP *SISH SISH

`SISH.SETTING.H`を独自にカスタマイズした場合は他の人とバッティングしないために自分用の名前でカタログ化して下さい。

#### Linuxに接続時の注意点

SISHでは`Ctrl`+`Z`と`Ctrl`+`S`をショートカットキーとして使用します。 

Linuxでは以下の制御コードと重複しているので抑制する必要があります。

    stty stop undef
    stty susp ^K

## 機能

### コマンドライン入力時の単語補完入力機能

単語入力途中に`Tab`で下記の単語を補完、もう一度`Tab`で候補リストを表示します

- VOC内の `Verb`,`File`,`PAragraph`,`Keyword`など
- ファイル名が存在する場合にその辞書ファイルのフィールド
- フラットファイルの単語の次の単語の場合に限り、対象フラットファイルのレコードID
- `,`後のマルチパートファイル
- `LOGTO`コマンドの後は登録アカウントテーブルの内容から補完
- `%`後のL-Typeフィールドの辞書フィールド補完に対応(OpenQM)

### 英小文字大文字反転入力の切替機能

`Insert`キーでON/OFFを切り替えます

### カラーテーマの切替機能

`F11`キーでカラーテーマを切り替える事ができます。(OSCによるカラーパレット変更に対応している端末のみ)
パレットは以下のサイトのSolarized-lightとdarkが利用できます。
http://ethanschoonover.com/solarized

### ターミナルサイズの自動変更機能

ターミナルクライアントのサイズを取得してサイズが違っていた場合に`TERM`コマンドで自動的にサイズ調整を行います。 

デフォルトでは自動変更モードになっています。 
`TERM`コマンドでターミナルサイズを設定した場合は自動変更モードは解除されます。 

自動変更モードに戻す際は、`TERM AUTO`コマンドを実行します。

### コマンド実行時間計測機能

`.T`コマンドで時間計測機能をON/OFF

### スクリーンエディタ

シェルにて以下のコマンドにて起動します。

`.E [File] [Item] [FieldId...]`

> `[Item]`は選択リスト時に省略可能です。
> `[FieldId]`を指定すると指定フィールドを辞書に基づいて編集するモードで起動します。行数表示横にフィールド名が表示されます。Conversion指定があれば保存時に自動的に変換されます。マルチバリューの場合はマルチバリュー編集モードになります。

デフォルトのキー操作はWindowsの一般的なテキストエディタと同様です。

メモリが許す限り無制限にUndo/Redoできます。コマンドライン内でも同様に動作します。

マウスは使用することが出来ませんが、`Ctrl`を押しながらのキー移動や高機能なジャンプ機能によりキーボードによる効率的なカーソル移動が可能となっています。

マルチバッファ機能にて同時に複数のItemを編集できます。`Ctrl`+`B`でバッファ一覧ポップアップが開きますので切り替えたいアイテムを選択してください。 

バッファは明示的に閉じるまでセッションメモリ（COMMON）に格納され続けます。

`ESC`キーで画面の下部にメニューが表示されます。メニューの内のテキストで大文字で表示されているキーを押すとそのメニューを選択できます。

`Ctrl`+`O`でラベル一覧ポップアップが表示されます。ラベルは実行コードには入らないのでGoToなどで使用しなくてもソースコード上に書いておけば簡単に目的の場所にジャンプすることができます。

BASICプログラムは構文が装飾されて表示されます。分岐やループで自動的にインデントが増えます。

※エディタ使用中にターミナルエミュレータのサイズを小さくした場合は、表示が崩れますのでその時は一度エディタを抜けてから戻ってきて下さい。`Ctrl+E` `Ctrl+E`

## キー別機能一覧

### シェル/エディタ共通

#### カーソル操作

| キー| 機能 |
|-----|------|
| `→` | 右移動 |
| `←` | 左移動 |
| `↑` | 上移動 |
| `↓` | 下移動 |
| `Ctrl`+`→` |  次の単語に移動<br>カーソルが対応する括弧上にある場合は対応する括弧まで移動 |
| `Ctrl`+`←` |  前の単語に移動<br>カーソルが対応する括弧上にある場合は対応する括弧まで移動 |
| `Ctrl`+`↑` |  3行前に移動（先が非表示文字の場合は表示文字） |
| `Ctrl`+`↓` |  3行先に移動（先が非表示文字の場合は表示文字） |
| `HOME` |  論理行頭/物理行頭へ移動 |
| `END` |  行末へ移動 |
| `Ctrl`+`HOME` |  データの先頭へ移動 |
| `Ctrl`+`END` |  データの末尾へ移動 |
| `PgUp` |  半ページ戻る |
| `PgDn` |  半ページ進む |
| `Ctrl`+`PgUp` |  前のページ戻る |
| `Ctrl`+`PgDn` |  次のページ進む |
| `Ctrl`+`.` |  続けて入力した1文字が次に出現する位置に移動<br>連続で同じ文字を入力するとさらに次に出現する位置に移動 |
| `Ctrl`+`,` |  続けて入力した1文字が遡って次に出現する位置に移動<br>連続で同じ文字を入力するとさらに次に出現する位置に移動 |

※上記にさらに`Shift`同時押しで範囲選択します

カーソル移動早見表

    　　　　　　　　　　　　　　<Ctrl+Home>
    　　　　　　　　　　　　　　　　 |
    　　　　　　　　　　　　　　<Ctrl+PgUp>
    　　　　　　　　　　　　　　　　 |
    　　　　　　　　　　　　　　　<PgUp>
    　　　　　　　　　　　　　　　　 |
    　　　　　　　　　　　　　　 <Ctrl+↑>
    　　　　　　　　　　　　　　　　 |
    　　　　　　　　　　　　　　　　<↑>
    　　　　　　　　　　　　　　　　 |
    　<Home> -- <Ctrl+←> -- <←> --|-- <→> -- <Ctrl+→> -- <End>
    　　　　　　　　　　　　　　　　 |
    　　　　　　　　　　　　　　　　<↓>
    　　　　　　　　　　　　　　　　 |
    　　　　　　　　　　　　　　 <Ctrl+↓>
    　　　　　　　　　　　　　　　　 |
    　　　　　　　　　　　　　　　<PgDown>
    　　　　　　　　　　　　　　　　 |
    　　　　　　　　　　　　　<Ctrl+PgDown>
    　　　　　　　　　　　　　　　　 |
    　　　　　　　　　　　　　　<Ctrl+End>

#### 編集操作

|キー|機能|
|----|----|
|`Ctrl`+`Z` | 直前の編集操作を戻す(アンドゥ) |
|`Ctrl`+`Y` | 直前の編集操作を戻す(リドゥ) |
|`Ctrl`+`X` | カット |
|`Ctrl`+`C` | コピー |
|`Ctrl`+`V` | クライアント端末のクリップボードから貼り付け |
|`Alt`+`V` | サーバローカルのクリップボードから貼り付け |
| `Ctrl`+`A` |  選択領域の拡張（押すたびに以下を繰り返します）<br>`選択なし`->`単語選択`->`空白文字まで選択`->`1行選択`->`同一インデント行を選択`->`全行選択`->`選択なし`|

#### その他操作

|キー|機能|
|----|----|
|`F11` | テーマ切り替え |
|`F1` | カーソル上の単語をヘルプ表示 |

### シェルのみ

|キー|機能|
|----|----|
|`↑` | コマンド履歴戻る |
|`↓` | コマンド履歴進む |
|`Ctrl`+`R` | コマンド履歴を古い方にインクリメンタルサーチ |
|`Ctrl`+`S` | コマンド履歴を新しい方にインクリメンタルサーチ |
|`Insert` | 大文字/小文字　反転入力の切り替え |
|`TAB` | 1回押下で共通部分の文字補完、２回押下で補完候補をリスト表示 |
|`PgUp` | 補完候補リストのページ戻り |
|`PgDn` | 補完候補リストのページ送り |
|`Ctrl`+`T` | 直前のコマンドのファイル名を挿入 |
|`Ctrl`+`E` | エディタ画面に遷移 |

### エディタのみ

`CHAR(27)`=(`ESC` or `Ctrl`+`[` or `Ctrl`+`3`) or `F10`でメニューを表示

#### カーソル操作

|キー|機能|
|----|----|
|`Ctrl+N`|カーソル行を画面の中央にスライドさせます|

#### 編集

|キー|機能|
|----|----|
|`TAB` | 選択領域のインデントを増やす |
|`Shift`+`TAB` | 選択領域のインデントを減らす |
|`Ctrl`+`5` | @VMの挿入 |
|`Ctrl`+`4` | @SVMの挿入 |
|`Ctrl`+`Del` | 行削除 |
|`Ctrl`+`!` | 選択領域のコメント化/コメント化解除 |

#### ジャンプ

|キー|機能|
|----|----|
|`Ctrl`+`F` | 単語検索 |
|`F3` | 直前の単語検索をもう一度実行 |
|`Ctrl`+`F3` | 直前の単語検索を逆戻りで実行 |
|`Ctrl`+`R` | 単語置換 |
|`Ctrl`+`L` | 行番号指定ジャンプ |
|`Ctrl`+`G` or `F12` | 定義元へジャンプ |
|`Ctrl`+`O` | アウトライン(ラベル一覧)ポップアップ表示 |
|`Ctrl`+`B` | バッファ一覧ポップアップ表示 |
|`Alt`+`→` | ジャンプ履歴進む |
|`Alt`+`←` | ジャンプ履歴戻る |

#### ファイル

|キー|機能|
|----|----|
|`Ctrl+S` | 上書き保存 |
|`F5` | リロード |
|`Ctrl`+`F4` | Bufferを閉じる |
|`F7` | コンパイル |
|`F4` | 下位レベルにDive(カーソル行の内容を下位の区切りレベルの編集モードとして新しいバッファに開きます) |

## 制限事項

*   UniVerse版ではコマンド実行中にAbortすると`SISH`もAbortします。

## おまけ

おすすめのフォント設定は Consolas + MeiryoKe_Console です。

以下のサイトに詳しい導入方法が載っています。 
[http://d.hatena.ne.jp/amachang/20111226/1324874731](http://d.hatena.ne.jp/amachang/20111226/1324874731)

以下は適応した場合の画面キャプチャー 
![](./SISH Manual_files/Consolas.png)

導入方法は少しややこしいですが、MSゴシックより見やすくなるので対応する価値はあるかと思います。

## 更新履歴

### 2016/07/07 0.9.0リリース

- シェル機能
	- L-Typeの辞書フィールド補完に対応 (OpenQM)
	- Basic系コマンド利用時にBPを省略するとBP.OUTの内容を補完するようになりました。
	- Ctrl+Rでのコマンド履歴のインクリメンタルサーチが出来るようになりました。

- エディタ機能
	- ITEM編集時にカーソル上のフィールドでF4キーを押すとマルチバリューを１行として編集するモードに移行する機能を追加
	- エディタ機能のコマンドにてフィールドを指定することで辞書駆動編集に対応
		- マルチバリューの同一アソシエーションを指定する事で連動して編集することができます。
		- Conversion等も自動的に変換されて保存されます。
	- ステータスバーにカーソル上のキャラコードを表示
	- 改行マークとタブを視認できるようにした
	- Ctrl+Aで選択領域を拡張していく仕様にした
	- 条件文やループ文の後に改行すると自動でインデントを設定するようにした

- キーシーケンスとカラーパレットの設定部分のソースを外だしにしました。

### 2014/09/10 0.8.0リリース

- 初回リリース

## TODO

- エディタ内単語の入力補完機能

## License

[GPLv2](./LICENSE)
