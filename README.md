以下は、もとの内容を Markdown 形式で整理し、見やすく編集した README です。適宜コピー＆ペーストしてご利用ください。

```markdown
# HackAichi リアルタイムカフェ空席モニタリングシステム

ESP32S3 搭載センサーが席の利用状況を検知し、Flask バックエンド（Google App Engine 上）へ送信。  
React フロントエンドで現在の空席状況や履歴を可視化するシステムです。

---

## 目次

1. [機能](#1-機能)  
2. [アーキテクチャ](#2-アーキテクチャ)  
3. [リポジトリ構成](#3-リポジトリ構成)  
4. [前提環境](#4-前提環境)  
5. [セットアップ＆インストール](#5-セットアップ＆インストール)  
   1. [ESP32S3 ファームウェア](#51-esp32s3-ファームウェア)  
   2. [バックエンドサーバ](#52-バックエンドサーバ)  
   3. [フロントエンドアプリ](#53-フロントエンドアプリ)  
6. [使い方](#6-使い方)  
7. [設定項目](#7-設定項目)  
8. [今後の改善予定](#8-今後の改善予定)  
9. [貢献者](#9-貢献者)  
10. [連絡先](#10-連絡先)  

---

## 1. 機能

- **リアルタイム空席表示**  
  各店舗の現在の空席数をダッシュボードで表示します。

- **履歴グラフ**  
  過去24時間の利用推移をチャート表示します。

- **店舗除外機能**  
  メンテナンス中などの店舗を一覧から除外できます。

- **データエクスポート**  
  現在値・履歴データを JSON 形式でダウンロードできます。

---

## 2. アーキテクチャ

```

\[ESP32S3センサー]
↓ HTTP POST（JSON）
\[FlaskバックエンドAPI] ── Google App Engine
↑ WebSocket／HTTP
\[ReactフロントエンドSPA]

```

- **ESP32S3**  
  - 顔認証カメラ ＋ 超音波センサーで入退店・席利用を検知  
  - 定期的に JSON 形式でバックエンドへ送信

- **バックエンド（Python + Flask）**  
  - `analysis.py` でデータ受信・集計・保存  
  - `app.yaml` で Google App Engine（GAE）向けのデプロイ設定

- **フロントエンド（TypeScript + React）**  
  - バックエンドからライブデータを取得  
  - ダッシュボードやグラフを描画して表示

---

## 3. リポジトリ構成

```

/
├ .idea/                 （IDE 設定ファイル等）
├ ESP32S3/               （センサー用ファームウェア）
│   ├ platformio.ini
│   └ src/
│       └ main.cpp
├ BackEnd/               （Flask バックエンド）
│   ├ analysis.py
│   ├ requirements.txt
│   └ app.yaml
├ frontend/              （React SPA）
│   ├ public/
│   └ src/
├ .gitignore
└ README.txt

````

---

## 4. 前提環境

- **ESP32S3 開発環境**  
  - Arduino IDE または PlatformIO
- **Python**  
  - バージョン 3.8 以上
- **Node.js & npm**  
  - Node.js 14 以上
- **Google Cloud SDK**  
  - App Engine へのデプロイに必要

---

## 5. セットアップ＆インストール

### 5.1 ESP32S3 ファームウェア

1. ターミナルでプロジェクトのルートディレクトリに移動し、ESP32S3 フォルダへ移動：
   ```bash
   cd ESP32S3
````

2. `src/main.cpp` 内の以下の部分を編集して、自身の Wi-Fi 情報とバックエンド URL を設定：

   ```cpp
   #define WIFI_SSID       "YOUR_SSID"
   #define WIFI_PASSWORD   "YOUR_PASSWORD"
   #define BACKEND_URL     "https://<YOUR_BACKEND_URL>"
   ```
3. ビルド＆アップロード：

   ```bash
   pio run --target upload
   ```

---

### 5.2 バックエンドサーバ

1. `BackEnd` ディレクトリへ移動：

   ```bash
   cd BackEnd
   ```

2. 仮想環境の作成＆有効化：

   ```bash
   python -m venv venv
   source venv/bin/activate   # Windows の場合は `venv\Scripts\activate`
   ```

3. 依存パッケージをインストール：

   ```bash
   pip install -r requirements.txt
   ```

4. **ローカル起動（テスト用）**：

   ```bash
   python analysis.py
   ```

   * デフォルトではポート `8080` で起動します。

5. **Google App Engine へのデプロイ**：

   * 事前にプロジェクトを作成し、`gcloud` CLI で認証済みであることを確認しておく。

   ```bash
   gcloud config set project YOUR_PROJECT_ID
   gcloud app deploy app.yaml
   ```

---

### 5.3 フロントエンドアプリ

1. `frontend` ディレクトリへ移動：

   ```bash
   cd frontend
   ```

2. パッケージをインストール：

   ```bash
   npm install
   ```

3. 必要に応じて、`src/config.ts` 内の API エンドポイントを設定：

   ```ts
   export const API_BASE_URL = "https://<YOUR_BACKEND_URL>";
   ```

4. **開発サーバ起動**：

   ```bash
   npm start
   ```

   * ブラウザで `http://localhost:3000` にアクセスできます。

5. **本番ビルド**：

   ```bash
   npm run build
   ```

   * `build/` ディレクトリに最適化されたファイルが生成されます。

---

## 6. 使い方

1. **ESP32S3 を起動**し、Wi-Fi に接続させます。
2. **バックエンド（Flask）** のログで JSON 受信が行われているか確認します。
3. ブラウザで **フロントエンドアプリ** にアクセスし、空席状況と履歴を確認します。
4. 必要に応じて、各店舗の「除外」チェックをオンにして表示を切り替えます。
5. 「JSON エクスポート」ボタンから、最新データや履歴データをダウンロードできます。

---

## 7. 設定項目

* **ESP32S3（`src/main.cpp`）**

  * `WIFI_SSID`
  * `WIFI_PASSWORD`
  * `BACKEND_URL`

* **バックエンド（`analysis.py`）**

  * ポート設定（デフォルト：8080）

* **Google App Engine（`app.yaml`）**

  * プロジェクト ID
  * サービス名等

* **フロントエンド（`src/config.ts`）**

  * `API_BASE_URL`（バックエンドのベース URL）

---

## 8. 今後の改善予定

* 満席／空席アラートのプッシュ通知機能を追加
* モバイルアプリ化（React Native などを検討）
* AI を用いた利用予測機能の実装
* UI/UX の強化（ショートカット機能、複数画像表示など）

---

## 9. 貢献者

* 玉城（@gusuku-oknw）
* 岡田（@otake-code）
* 田上
* 立岩

---

## 10. 連絡先

ご質問やご意見は以下までお問い合わせください。

* **メールアドレス**: [gusuku.oknw@example.com](mailto:gusuku.oknw@example.com)

---

**Enjoy monitoring!**
