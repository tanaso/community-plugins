# News Pigeon Community Plugins

[News Pigeon](https://github.com/tanaso/NewsPigeon) のコミュニティプラグイン中央カタログです。
Obsidian の [obsidian-releases](https://github.com/obsidianmd/obsidian-releases) と同じ役割を持ちます。

アプリはこのリポジトリの `community-plugins.json` を取得してプラグイン一覧を表示し、
各エントリの `repo` が指す GitHub リポジトリの **最新 Release のアセット** から
プラグイン本体をダウンロードします。コードの実体はこのリポジトリには置きません。

## カタログ形式（community-plugins.json）

軽量エントリの配列です。

```json
[
  {
    "id": "np-hello",
    "name": "NP Hello",
    "author": "Example Author",
    "description": "A community plugin",
    "repo": "owner/repository"
  }
]
```

| フィールド | 必須 | 説明 |
| --- | --- | --- |
| `id` | ✔ | プラグイン ID。小文字英数字とハイフン。`manifest.json` の `id` と一致必須 |
| `name` | ✔ | 表示名 |
| `author` | | 作者名 |
| `description` | | 一覧に表示される短い説明 |
| `repo` | ✔ | GitHub の `owner/repository` 形式 |

## プラグインの登録手順（作者向け）

1. GitHub リポジトリでプラグインを開発する。
2. リリース（タグ）を作成し、**Releases に以下をアセットとして添付**する:
   - `manifest.json`（必須）
   - `main.js`（`type: "js"` のプラグインは必須。ES Module で `onload`/`onunload` を export）
   - `styles.css`（任意。プラグイン有効化中のみアプリ全体に注入される）
3. このリポジトリに Pull Request を送り、`community-plugins.json` にエントリを 1 件追加する。

### manifest.json の例

```json
{
  "id": "np-hello",
  "name": "NP Hello",
  "version": "1.0.1",
  "minAppVersion": "1.0.0",
  "author": "Example Author",
  "description": "A community plugin",
  "permissions": ["ui.toast", "reader.transform"]
}
```

- `id` / `name` / `version` は必須。`version` は `1.2.3` 形式（リリースのたびに上げる）。
- `minAppVersion` を指定すると、それ未満のアプリにはインストール不可と表示されます。
- `type` を省略すると `"js"`、`entry` を省略すると `"main.js"` として扱われます。
- `permissions` に宣言した権限はインストール時にユーザーへ提示され、同意が必要です。
  更新で権限が増えた場合は増分について再同意が求められます。

詳細はアプリ本体の開発者ドキュメント
[コミュニティプラグイン配布ガイド](https://github.com/tanaso/NewsPigeon/blob/main/doc/guides/plugin-distribution.md)
を参照してください。

## セキュリティ

- プラグインコードは Web Worker サンドボックス内で実行され、`fetch` 等の
  アンビエント権限は剥奪されています。宣言した権限の API のみ使えます。
- ダウンロードした資産は端末内 SQLite に SHA-256 と共に保存されます。
- コミュニティプラグインは既定で無効（制限モード）であり、ユーザーが明示的に
  有効化しない限り実行されません。
