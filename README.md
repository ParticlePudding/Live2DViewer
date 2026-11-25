# Live2D ビューア（単体公開版）

ブラウザで Live2D モデルを表示・操作するビューアです。
このフォルダをそのまま静的サイトとして公開すると、Android の Chrome で「小窓（ピクチャー・イン・ピクチャー）」表示が使えます。

## GitHub Pages で公開する手順

1. GitHub で新しいリポジトリを作る（例: `live2d-viewer`）。
2. このフォルダの中身（`index.html`・`samples/`・`README.md`）をそのままアップロード（Add file → Upload files）。
3. リポジトリの Settings → Pages → Source を「Deploy from a branch」、Branch を `main` / `(root)` にして Save。
4. 1〜2分後に表示される `https://<ユーザー名>.github.io/live2d-viewer/` を Android の Chrome で開く。

※ `index.html` をスマホで直接ファイルとして開くと、サンプルの読み込みが動きません。必ず https のサイトとして公開してください。

## 会話（読み上げ）機能

「会話」タブで、入力した文をキャラが読み上げます。口パク・字幕付き。

- **ブラウザ標準**: 端末内蔵の音声合成（Web Speech API）。追加の通信なし・即時。音声波形が取れないため口パクは近似です。
- **つくよみちゃん**: piper-plus によるブラウザ内ニューラル音声合成。初回のみ音声モデル約40MB＋日本語辞書約20MBを取得し、以後はブラウザに保存されます。音量に合わせた口の開閉に加え、音素（あいうえお）に合わせて口の形も変えます。

- **外部TTS**: VOICEVOX ENGINE 互換のサーバー（VOICEVOX / AivisSpeech Engine など）から音声だけを受け取ります。モーラ（音の単位）の長さ情報で母音に合わせた口パクをします。受信量は画面に表示されます。

### 外部TTS（VOICEVOX ENGINE）のつなぎ方

GitHub Pages（https）から使うには、サーバー側も **https** と **CORS 許可** が必要です。自宅PCで動かす例：

1. VOICEVOX ENGINE を CORS 許可付きで起動
   ```
   run.exe --cors_policy_mode all
   ```
   （許可元を絞るなら `--allow_origin https://particlepudding.github.io`）
2. Tailscale で https 公開（スマホも同じ tailnet に参加）
   ```
   tailscale serve --bg 50021
   ```
   表示された `https://<PC名>.<tailnet>.ts.net` を「接続先URL」に入れ、「接続して話者を取得」。
3. 「音質」を 16kHz / 12kHz にすると受信量を減らせます（WAV のため 16kHz で約32KB/秒の発話）。

音声を使う場合は「VOICEVOX:キャラ名」のクレジット表記と各キャラクターの利用規約に従ってください。

### 外部から話させる（エージェント連携用）

```js
// 同じページ内のスクリプトから
l2d.say("おかえりなさい！", { expression: 0, motion: "TapBody", engine: "piper" });
l2d.stop();
// 親ページなどから
iframe.contentWindow.postMessage({ type: "l2d.say", text: "こんにちは", engine: "ws" }, "*");
```

### つくよみちゃん音声の利用条件

音声モデルは「つくよみちゃんコーパス（CV.夢前黎）」を学習したものです（https://tyc.rei-yumesaki.net/material/corpus/ ）。
ソフトウェアで使う場合は利用者に見える形でのクレジット表記が必要で、つくよみちゃん以外のキャラクターの声として使う場合は事前に権利者への相談が必要です。一般公開する前に規約を確認してください。

## ライブラリ（CDN から読み込み）

- Live2D Cubism Core 5.0（Live2D Proprietary Software License）
- Cubism 2 runtime（live2d.min.js）
- PixiJS 7.4.2 / pixi-live2d-display-lipsyncpatch 0.5.0-ls-8 / JSZip 3.10.1
- piper-plus 0.9.1（MIT）/ onnxruntime-web 1.31.1（MIT）
- `vendor/piper-plus-wasm/`: piper-plus の日本語G2P WASM（NAIST-JDIC辞書入り・約60MB）。jsDelivr の1ファイル20MB上限を超えるため同梱。ライセンスは同フォルダ内の LICENSE.md / THIRD-PARTY-LICENSES.md を参照

## サンプルモデルについて

`samples/` のモデル（桃瀬ひより・ハル・虹色まお・マークくん・ライス・レン・わんころもち）は株式会社Live2Dの著作物です。
Live2D 公式リポジトリ CubismWebSamples から取得し、「無償提供マテリアルの使用許諾契約書」と「Live2D Cubism サンプルデータ利用条件」に基づいて同梱しています。
モデル本体（.moc3）は配信の都合で base64 テキスト（`*.moc3.b64.txt`）に変換してあります。データの中身は変更していません。

- 無償提供マテリアルの使用許諾契約書: https://www.live2d.com/eula/live2d-free-material-license-agreement_jp.html
- サンプルデータ利用条件: https://www.live2d.com/learn/sample/model-terms/

直近会計年度の売上高が1,000万円以上の事業者として公開・利用する場合は、Live2D 社との別途契約が必要です。

This content uses sample data owned and copyrighted by Live2D Inc.
