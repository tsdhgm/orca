# Ubuntu 26.04 向け .deb の作成

Ubuntu のビルド用マシンで、リポジトリのルートから実行します。
Node.js 24、`package.json` の `packageManager` に指定された pnpm、
`config/.bun-version` の Bun、および実行可能な Docker Engine が必要です。
Docker は Wayland クリップボード用バイナリを旧 glibc と互換性のある環境で
ビルドするために使用します。Docker の `hello-world` が一般ユーザーで動作する
状態にしてください。ビルドには依存ファイルを取得するネットワーク接続も必要です。

```bash
sudo apt update
sudo apt install build-essential python3 pkg-config binutils libx11-dev libsecret-1-dev
pnpm install --frozen-lockfile
pnpm -C mobile install --frozen-lockfile
npm run deb
```

`npm run deb` は既存のビルド処理でネイティブ部品、CLI、デスクトップ、
Web クライアントをビルドし、electron-builder で `.deb` のみを生成します。
GitHub への公開や署名用の認証情報は不要です。内部のビルドには pnpm を使うため、
依存関係のインストールには `npm install` ではなく上記の pnpm を使用してください。

生成先は `dist/orca-ide_<version>_<arch>.deb` です。
標準ではビルドマシンのアーキテクチャを使います（x64 → amd64、arm64 → arm64）。
ネイティブ依存関係を含むため、対象と同じアーキテクチャの Ubuntu マシンで
ビルドしてください。

生成されたファイル名を指定してインストールします。

```bash
sudo apt install ./dist/orca-ide_1.4.214_amd64.deb
```

アプリ一覧の Orca または `orca-ide` コマンドで起動できます。
Ubuntu のスクリーンリーダー `orca` と共存できるよう、パッケージ名と
コマンド名は `orca-ide` です。

ビルド時に glibc の互換性チェックに失敗した場合は、チェックを無効にせず、
表示されたネイティブ依存関係を既存の対応バージョンまたは互換ビルドに戻してください。
Ubuntu 26.04 上でコンパイルしたバイナリは、上流が維持している古い Linux 向けの
互換性条件を満たさない場合があります。
