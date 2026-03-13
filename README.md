# Paper → Calendar

紙に書かれたTODO・スケジュールをスマホのカメラで撮影 → AIが読み取り → Googleカレンダーに追加するPWA。

## セットアップ手順

### 1. OpenAI APIキーの取得

1. [OpenAI Platform](https://platform.openai.com/) にログイン
2. 左メニュー「API keys」から新しいキーを作成
3. `sk-` で始まるキーをコピー

### 2. Google Cloud プロジェクトの設定（初回のみ・約10分）

#### 2-1. プロジェクト作成

1. [Google Cloud Console](https://console.cloud.google.com/) にアクセス
2. 上部の「プロジェクト選択」→「新しいプロジェクト」
3. プロジェクト名（例: `paper-todo-calendar`）を入力して「作成」

#### 2-2. Calendar API を有効化

1. 左メニュー「APIとサービス」→「ライブラリ」
2. 「Google Calendar API」を検索して選択
3. 「有効にする」をクリック

#### 2-3. OAuth 同意画面の設定

1. 左メニュー「APIとサービス」→「OAuth同意画面」
2. User Type: 「外部」を選択して「作成」
3. アプリ名（例: `Paper Calendar`）、ユーザーサポートメール、デベロッパーの連絡先を入力
4. 「スコープ」は特に追加不要（アプリ側で指定する）
5. 「テストユーザー」に自分と共有したい人のGmailアドレスを追加
6. 「保存して次へ」で完了

> **注意**: テストモードでは登録したGmailアカウントのみ利用可能です。これはチーム利用には十分です。

#### 2-4. OAuth クライアントIDの作成

1. 左メニュー「APIとサービス」→「認証情報」
2. 「+認証情報を作成」→「OAuthクライアントID」
3. アプリケーションの種類: 「ウェブ アプリケーション」
4. **承認済みの JavaScript 生成元** に以下を追加:
   - ローカルテスト: `http://localhost:8000`（またはローカルサーバーのURL）
   - 本番: デプロイ先のURL
5. 「作成」をクリック
6. 表示される **クライアントID**（`xxxx.apps.googleusercontent.com`）をコピー

### 3. アプリの起動

ローカルでHTTPサーバーを起動（カメラアクセスにはHTTPSまたはlocalhostが必要）:

```bash
cd 営業ツール/paper-todo-calendar/
python3 -m http.server 8000
```

ブラウザで `http://localhost:8000` にアクセス。

### 4. アプリの初期設定

1. 右上の ⚙️ アイコンから設定画面を開く
2. OpenAI APIキーを入力
3. Google クライアントIDを入力
4. 「設定を保存」

### 5. 使い方

1. 「Googleアカウントでログイン」をタップ → Google認証
2. カメラボタン 📷 をタップ → 紙を撮影
3. AIが読み取り結果を表示 → 追加する項目を選択・編集
4. 登録先カレンダーを選択
5. 「カレンダーに追加」をタップ → 完了

### 6. チームで共有する方法

1. Googleカレンダーで「共有カレンダー」を作成
2. チームメンバーをそのカレンダーに招待
3. アプリで登録先にその共有カレンダーを選択
4. → チーム全員のGoogleカレンダーにTODOが反映される

### 7. スマホのホーム画面に追加（PWA）

- **iPhone**: Safari で開く → 共有ボタン → 「ホーム画面に追加」
- **Android**: Chrome で開く → メニュー → 「ホーム画面に追加」

## ファイル構成

```
paper-todo-calendar/
├── index.html       ... メインアプリ
├── manifest.json    ... PWA設定
├── sw.js            ... Service Worker（オフラインキャッシュ）
├── icon-192.png     ... アプリアイコン
├── icon-512.png     ... アプリアイコン（大）
└── README.md        ... このファイル
```

## 技術スタック

- HTML + CSS + JavaScript（フレームワーク不使用）
- OpenAI GPT-4 Vision API（画像認識 + 内容解析）
- Google Identity Services（OAuth 2.0 認証）
- Google Calendar API v3（イベント作成）
- PWA（Service Worker + manifest.json）

## API費用の目安

- GPT-4o: 1回の撮影あたり約5〜15円（画像サイズによる）
- GPT-4o-mini: 1回の撮影あたり約1〜3円
- Google Calendar API: 無料
