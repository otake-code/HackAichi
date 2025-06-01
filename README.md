
# HackAichi リアルタイムカフェ空席モニタリングシステム

ESP32S3搭載センサーが席の利用状況を検知し、Flaskバックエンド（Google App Engine上）へ送信。
Reactフロントエンドで現在の空席状況や履歴を可視化することができるシステムです。


## 目次

1. [概要](#概要)
2. [機能](#機能)
3. [アーキテクチャ](#アーキテクチャ)
4. [リポジトリ構成](#リポジトリ構成)
5. [前提環境](#前提環境)
6. [セットアップ＆インストール](#セットアップインストール)

   1. [ESP32S3 ファームウェア](#esp32s3-ファームウェア)
   2. [バックエンドサーバ](#バックエンドサーバ)
   3. [フロントエンドアプリ](#フロントエンドアプリ)
7. [使い方](#使い方)
8. [設定項目](#設定項目)
9. [今後の改善予定](#今後の改善予定)
10. [貢献者](#貢献者)
11. [連絡先](#連絡先)

---

## 概要

* **プロジェクト名**：HackAichi リアルタイムカフェ空席モニタリングシステム
* **目的**：ESP32S3センサーでカフェの席利用状況を検知し、リアルタイムで空席情報を可視化することで、お客様や店舗スタッフが効率的に空席状況を把握できるようにする。
* **主な技術スタック**：

  * **ESP32S3（センサー側）**：顔認証カメラ＋超音波センサー
  * **バックエンド**：Python + Flask / Google App Engine
  * **フロントエンド**：TypeScript + React

---

## 機能

* **リアルタイム空席表示**

  * 各店舗の現在の空席数をダッシュボードで表示
* **履歴グラフ**

  * 過去24時間の利用推移をチャートで確認
* **店舗除外機能**

  * メンテナンス中など、任意の店舗を一覧から除外可能
* **データエクスポート**

  * 現在値・履歴データをJSON形式でダウンロード

---

## アーキテクチャ

```
[ESP32S3センサー]
       ↓ HTTP POST（JSON）
[FlaskバックエンドAPI] ── Google App Engine
       ↑ WebSocket／HTTP
[ReactフロントエンドSPA]
```

1. **ESP32S3**

   * 顔認証カメラ ＋ 超音波センサーで入退店・席利用を検知
   * 定期的にJSON形式でバックエンドへ送信

2. **バックエンド（Python + Flask）**

   * `analysis.py` でデータを受信・集計・保存
   * `app.yaml` でGoogle App Engine向けのデプロイ設定

3. **フロントエンド（TypeScript + React）**

   * バックエンドからライブデータを取得
   * ダッシュボードやグラフを描画して表示

---

## リポジトリ構成

```
/
├ .idea/                 （IDE設定ファイル等）
├ ESP32S3/               （センサー用ファームウェア）
│   ├ platformio.ini
│   └ src/
│       └ main.cpp
├ BackEnd/               （Flaskバックエンド）
│   ├ analysis.py
│   ├ requirements.txt
│   └ app.yaml
├ frontend/              （React SPA）
│   ├ public/
│   └ src/
├ .gitignore
└ README.md
```

* `ESP32S3/`

  * センサー側のファームウェアおよびPlatformIOプロジェクト
* `BackEnd/`

  * Flaskアプリケーション一式
  * `analysis.py`：受信・集計・保存ロジック
  * `requirements.txt`：Python依存パッケージ
  * `app.yaml`：GAEデプロイ設定
* `frontend/`

  * Reactアプリケーション一式
* `.gitignore`

  * Git管理外とするファイルを指定
* `README.md`

  * 本ドキュメント

---

## 前提環境

* **ESP32S3 開発環境**

  * Arduino IDE または PlatformIO
* **Python**

  * バージョン 3.8 以上
* **Node.js & npm**

  * Node.js 14 以上
* **Google Cloud SDK**

  * App Engineへのデプロイに必要

---

## セットアップ＆インストール

### ESP32S3 ファームウェア

1. プロジェクトのルートディレクトリへ移動し、ESP32S3フォルダへ移動します：

   ```bash
   cd ESP32S3
   ```

2. `src/main.cpp` 内の下記定義を、自身のWi-Fi情報とバックエンドURLに書き換えます：

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

### バックエンドサーバ

1. `BackEnd`ディレクトリへ移動：

   ```bash
   cd BackEnd
   ```

2. 仮想環境を作成＆有効化：

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

5. **Google App Engineへのデプロイ**

   * 事前にGCPプロジェクトを作成し、`gcloud`で認証済みであることを確認してください。

   ```bash
   gcloud config set project YOUR_PROJECT_ID
   gcloud app deploy app.yaml
   ```

---

### フロントエンドアプリ

1. `frontend`ディレクトリへ移動：

   ```bash
   cd frontend
   ```

2. 依存パッケージをインストール：

   ```bash
   npm install
   ```

3. 必要に応じて、`src/config.ts` 内の APIエンドポイントを設定：

   ```ts
   export const API_BASE_URL = "https://<YOUR_BACKEND_URL>";
   ```

4. **開発サーバ起動**：

   ```bash
   npm start
   ```

   * ブラウザで `http://localhost:3000` を開くと、開発用画面が表示されます。

5. **本番ビルド**：

   ```bash
   npm run build
   ```

   * 最適化された静的ファイルが `build/` ディレクトリに生成されます。

---

## 使い方

1. **ESP32S3を起動**し、Wi-Fiに接続します。
2. \*\*バックエンド（Flask）\*\*のログにJSON受信があるか確認します。
3. ブラウザで**フロントエンドアプリ**にアクセスし、空席状況と履歴を確認します。
4. 必要に応じて、各店舗の「除外」チェックをONにして表示を切り替えます。
5. 「JSONエクスポート」ボタンから、最新データや履歴データをダウンロードできます。

---

## 設定項目

* **ESP32S3（`src/main.cpp`）**

  * `WIFI_SSID`
  * `WIFI_PASSWORD`
  * `BACKEND_URL`

* **バックエンド（`analysis.py`）**

  * ポート設定（デフォルト：8080）

* **Google App Engine（`app.yaml`）**

  * プロジェクトID
  * サービス名など

* **フロントエンド（`src/config.ts`）**

  * `API_BASE_URL`（バックエンドのベースURL）

---

## 今後の改善予定

* 満席／空席アラートのプッシュ通知機能追加
* モバイルアプリ化（React Nativeなどを検討）
* AI を用いた利用予測機能の実装
* UI/UX の強化（ショートカット機能、複数画像表示など）

---

## 貢献者

* 玉城（@gusuku-oknw）
* 岡田（@otake-code）
* 田上
* 立岩

---

## 連絡先

ご質問やご意見は以下までお問い合わせください。

* **メールアドレス**: [gusuku.oknw@example.com](mailto:gusuku.oknw@example.com)

---

**Enjoy monitoring!**
