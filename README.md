# blockly-for-led

<img src="https://github.com/kut-tktlab/blockly-for-led/wiki/blockly-screen.png"
 width="480" height="300" alt="screen capture" />&nbsp;
<a href="https://www.youtube.com/watch?v=Gf6LSokECh0"><img
src="https://raw.github.com/wiki/kut-tktlab/serial-led-pi/demo1.jpg" width="225" height="127"
alt="a link to a demo movie on youtube" /></a>

[シリアルLEDテープ](https://github.com/kut-tktlab/serial-led-pi/)
の制御のための[Blockly](https://developers.google.com/blockly/)アプリケーション。

ブロックの配色やツールボックスのデザインは[micro:bit](http://microbit.org/)
[JavaScript Blocks Editor](https://makecode.microbit.org/)及び[chibi:bit IDE](http://chibibit.io/ide/)
を真似ています。


## お試し

[デモページ](https://ytakata69.github.io/blockly-for-led/)でお試しできます
(手動同期なので最新版でないことがあります)。

ブラウザ上で動作を確認するためのLEDシミュレータが付いています。

## Install

LEDシミュレータを使う場合は，
`git clone` して `index.html` をブラウザで開いてください。

実LEDを使う場合は，LEDテープを繋いだRaspberry Piに
[Node.js](https://nodejs.org) (実行環境),
[npm](https://www.npmjs.com) (パッケージマネージャ),
[node-gyp](https://github.com/nodejs/node-gyp) (ビルドツール)
をインストールし，
[serial-led-pi](https://github.com/kut-tktlab/serial-led-pi/)
のNode.js用アドオンをビルドして，
`sudo node server.js` を実行してください (サーバの起動)。
その後，ブラウザで `http://ラズパイのアドレス:8080/` を開いてください。
ブラウザはRaspberry Pi上で動かしても別の機械で動かしても構いません。  
(詳しい手順は
[Wiki](https://github.com/kut-tktlab/blockly-for-led/wiki/Connecting-to-Serial_Led_Pi)
を参照してください。)

## Files

- `index.html` -- クライアント側のメインファイル。CSSファイル / JavaScriptファイルをロードし，Blocklyを動かす。
- `blockly/` -- [Blockly](https://developers.google.com/blockly/) のファイル群
  - リポジトリが大きくなるため，Blocklyの履歴は継承していません。[配布元](https://github.com/google/blockly)を参照してください。
  - 同様に [Demos](https://blockly-demo.appspot.com/static/demos/index.html) も削除しています。[配布元](https://github.com/google/blockly)を参照してください。
  - 施した改造は，メッセージと配色の変更 (`blockly/msg/`)，LED用ブロック及び「最初だけ」「ずっと」「○ミリ秒待つ」ブロックの追加 (`blockly/blocks/`)，追加したブロックのためのコード生成部の追加 (`blockly/generators/`) などです。
- `ledsimulator.js` -- LEDシミュレータのプログラム。`index.html` にロードされる。
- `coderunner.js` -- Blocklyで作ったプログラム (ブロック) を実行するプログラム。`index.html` と `server.js` の両方にロードされる。
  - LEDシミュレータモードのときは，クライアント側で動き，`ledsimulator.js` を呼び出すプログラムとして働く。
  - 実LEDモードのときは，クライアント側ではサーバと通信をするプログラムとして働き，サーバ側では[serialled](https://github.com/kut-tktlab/serial-led-pi/)アドオンを呼び出すプログラムとして働く。
- `server.js` -- サーバ側のメインファイル
- `example/` -- 「プログラム例」ボタンを押したときに表示されるサンプルプログラム群。詳しくは `example/README.md` を参照。
- `package.json` -- `npm install` で [socket.io](https://socket.io) がインストールされるようにするための情報ファイル

LEDシミュレータモードのときはクライアントサイドだけで動きます。
`index.html` と CSS / JavaScript ファイル群がロードできればよいので，ローカルファイルとして開くのでも，
[デモページ](https://ytakata69.github.io/blockly-for-led/)のようにWebサーバ経由でロードするのでも構いません

実LEDモードのときは，Runボタンを押すまではLEDシミュレータモードと同じで，
Runボタンを押したときはプログラム (ブロック) の実行をサーバに依頼します。
サーバとの通信には [WebSocket](http://www.ietf.org/rfc/rfc6455.txt) (正確には [socket.io](https://socket.io)) を使います。
サーバは[Node.js](https://nodejs.org/ja/)アプリケーションとして実装されていて，
[serialled](https://github.com/kut-tktlab/serial-led-pi/)アドオンを呼び出してLEDテープを制御します。

LEDシミュレータモードと実LEDモードの判別: 
`index.html` がローカルファイルでなく (URLが `file://...` でなく)，かつ
socket.io-client がロードできた (インストールされていた) 場合に実LEDモードで動作します。

## 改造

[Blockly Guides](https://developers.google.com/blockly/guides/overview)
の各ページを参照してください。

- Configure Blockly - Blocklyを改造せずに (index.html上で) できるカスタマイズ
- Create Custom Blocks - 新しいブロックを定義する
- Modify Blockly &gt; Web &gt; Building - Blocklyのファイル構成

Blocklyを改造した場合，以下のどちらかの作業が必要です。

1. `build.py` を実行して `blockly_compressed.js` 等を再構築する。
2. index.html にて，`blockly_compressed.js` 等の代わりに `core/`, `blocks/`
等の下の個々のjsファイルをロードする
(詳しくは上記Guidesの[Building](https://developers.google.com/blockly/guides/modify/web/building)を参照)。

`build.py` を実行する場合は
[Closure Library](https://developers.google.com/closure/library/)
が必要です。
zipファイルをダウンロードして解凍し，`blockly`の親ディレクトリに
(`blockly`から `../closure-library/` に見えるように) 置いてください。
なお，`./build.py` の実行には1分弱かかります。

## リンク
- [serial-led-pi](https://github.com/kut-tktlab/serial-led-pi/) - 
  A simple library for controlling LED-strips and rings with Raspberry Pi
- [Blockly | Google Developers](https://developers.google.com/blockly/) -
  A library for building visual programming editor
- [BBC micro:bit](http://microbit.org/) | [makecode.microbit.org](https://makecode.microbit.org/) |
  [chibi:bit IDE](http://chibibit.io/ide/)
