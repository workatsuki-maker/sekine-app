# 2代目関根勤選手権

バラエティ番組風の参加型ウェブサービスです。  
AI生成の人物写真を見て「ピッタリの一言」を投稿し、いいね数でランキングを競います。

## ファイル構成

```
sekine-app/
├── index.html   # アプリ本体（これ1ファイルで動作します）
└── README.md    # このファイル
```

---

## Supabase の設定値を貼る場所

`index.html` をテキストエディタで開き、**9〜10行目付近**（`<script>` タグの直下）にある以下の2行を見つけてください。

```javascript
const SUPABASE_URL = 'ここにSupabaseのProject URLを貼る';
const SUPABASE_ANON_KEY = 'ここにSupabaseのAnon Keyを貼る';
```

### 設定値の確認方法

1. [Supabase ダッシュボード](https://supabase.com/dashboard) にログイン
2. 対象のプロジェクトを開く
3. 左メニューの **「Settings」→「API」** を選択
4. 以下の2つの値をコピーして貼り付けてください

| 変数名 | Supabase上の項目名 |
|---|---|
| `SUPABASE_URL` | **Project URL**（例: `https://xxxxxxxx.supabase.co`） |
| `SUPABASE_ANON_KEY` | **Project API keys → anon / public** |

### 貼り付け後のイメージ

```javascript
const SUPABASE_URL = 'https://xxxxxxxxxxxxxxxx.supabase.co';
const SUPABASE_ANON_KEY = 'eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.xxxxxxxx...';
```

---

## Supabase のテーブル構成

アプリが正しく動作するには以下のテーブルが必要です。

### `photos` テーブル

| カラム名 | 型 | 説明 |
|---|---|---|
| `id` | int8 (Primary Key) | 自動採番 |
| `storage_path` | text | Storage内のパス（例: `persons/001.jpg`） |
| `created_at` | timestamptz | 作成日時（自動） |

### `posts` テーブル

| カラム名 | 型 | 説明 |
|---|---|---|
| `id` | int8 (Primary Key) | 自動採番 |
| `photo_id` | int8 (FK → photos.id) | 対象写真のID |
| `content` | text | 投稿テキスト（最大50文字） |
| `likes_count` | int4 | いいね数（デフォルト: 0） |
| `created_at` | timestamptz | 作成日時（自動） |

### `likes` テーブル

| カラム名 | 型 | 説明 |
|---|---|---|
| `id` | int8 (Primary Key) | 自動採番 |
| `post_id` | int8 (FK → posts.id) | 対象投稿のID |
| `session_id` | uuid | ユーザーのセッションID |
| `created_at` | timestamptz | 作成日時（自動） |

**重要:** `likes` テーブルには `(post_id, session_id)` の **Unique 制約** を設定してください（二重いいね防止のため）。

---

## Supabase Storage の設定

### バケット名

アプリは **`persons`** という名前のバケットを使用します。

1. Supabase ダッシュボードの **「Storage」** を開く
2. **「New bucket」** で `persons` という名前のバケットを作成
3. **「Public bucket」** にチェックを入れて公開設定にする

### 写真のアップロード方法

1. ダッシュボードの Storage → `persons` バケットを開く
2. `persons/` フォルダ直下に画像ファイルをアップロード
3. `photos` テーブルに対応する行を追加（`storage_path` に `persons/ファイル名.jpg` を記入）

写真は後から追加するだけで自動的にアプリに反映されます。

---

## 動作確認

1. `index.html` をブラウザで直接開く（または VS Code の Live Server で起動）
2. 写真が表示されることを確認
3. 一言を入力して「投稿する」ボタンを押し、ランキングに表示されることを確認
4. ハートボタンをクリックしていいねのトグルを確認
5. 「次の人 →」「← 前の人」ボタンで写真が切り替わることを確認
