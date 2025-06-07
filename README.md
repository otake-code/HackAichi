# HotAICafe - リアルタイムカフェ空席モニタリングシステム

ESP32-S3搭載センサーが席の利用状況を検知し、AIによるパーソナライズ表示とマイコンによるリアルタイム通信で、空席情報を可視化するシステムです。

## 目次

1. [概要](#概要)
2. [機能](#機能)
3. [アーキテクチャ](#アーキテクチャ)
4. [リポジトリ構成](#リポジトリ構成)
5. [前提環境](#前提環境)
6. [セットアップ＆インストール](#セットアップインストール)

   1. [ESP32-S3 ファームウェア](#esp32-s3-ファームウェア)
   2. [バックエンドサーバ](#バックエンドサーバ)
   3. [フロントエンドアプリ](#フロントエンドアプリ)
7. [使い方](#使い方)
8. [設定項目](#設定項目)
9. [今後の改善予定](#今後の改善予定)
10. [貢献者](#貢献者)
11. [連絡先](#連絡先)

---

## 概要

* **プロジェクト名**：HotAICafe リアルタイムカフェ空席モニタリングシステム
* **目的**：ESP32-S3センサーでカフェ席の入退店を検知し、AIで最適化したパーソナライズ表示とマイコンによるリアルタイム更新を可能にすることで、利用者と店舗スタッフ双方の利便性を高めます。
* **主な技術スタック**:

  * **ESP32-S3（センサー側）**：顔認証カメラ＋超音波センサー
  * **バックエンド**：Python + Flask（Google App Engine）
  * **フロントエンド**：TypeScript + React

## 機能

* **AIによるパーソナライズ表示**

  * 利用者の過去の行動履歴や好みに応じた席提案
  * 人気席や推奨席をハイライト
* **マイコンによるリアルタイム更新**

  * ESP32-S3からのセンサーデータを即時反映
  * WebSocketでフロントエンドにライブ配信
* **リアルタイム空席表示**

  * ダッシュボードで現在の空席数を表示
* **履歴グラフ**

  * 過去24時間の座席利用推移をチャートで確認
* **店舗除外機能**

  * メンテナンス中店舗の一覧からの除外
* **データエクスポート**

  * JSON形式で現在値・履歴データをダウンロード

## アーキテクチャ

```
[ESP32-S3センサー]
       ↓ HTTP POST（JSON）
[Flaskバックエンド API] ── Google App Engine
       ↑ WebSocket／HTTP
[Reactフロントエンド SPA]
```

1. **ESP32-S3**

   * 顔認証カメラ＋超音波センサーで入退店と座席利用を検知
   * 定期的にJSONでバックエンドへ送信
2. **バックエンド（Python + Flask）**

   * データ受信・集計・保存
   * WebSocketでフロントへライブ送信
3. **フロントエンド（TypeScript + React）**

   * ライブデータの取得とダッシュボード表示
   * AIモデルを呼び出し、パーソナライズ結果を反映

## リポジトリ構成

```
/
├ ESP32-S3/            （センサー用ファームウェア）
│   ├ platformio.ini
│   └ src/
│       └ main.cpp
├ BackEnd/            （Flaskバックエンド）
│   ├ analysis.py
│   ├ requirements.txt
│   └ app.yaml
├ frontend/           （React SPA）
│   ├ public/
│   └ src/
├ .gitignore
└ README.md
```

## 前提環境

* **ESP32-S3**: PlatformIOまたはArduino IDE
* **Python**: 3.8以上
* **Node.js & npm**: Node.js 14以上
* **Google Cloud SDK**: App Engineデプロイ用

## セットアップ＆インストール

### ESP32-S3 ファームウェア

1. `ESP32-S3/` ディレクトリへ移動

   ```bash
   cd ESP32-S3
   ```
2. `src/main.cpp` にWi-Fi情報とバックエンドURLを設定
3. ビルド＆アップロード

   ```bash
   pio run --target upload
   ```

### バックエンドサーバ

1. `BackEnd/` ディレクトリへ移動
2. 仮想環境作成 & 依存パッケージインストール

   ```bash
   python -m venv venv
   source venv/bin/activate
   pip install -r requirements.txt
   ```
3. ローカル起動（ポート8080）
4. GAEデプロイ

   ```bash
   gcloud config set project YOUR_PROJECT_ID
   gcloud app deploy app.yaml
   ```

### フロントエンドアプリ

1. `frontend/` へ移動
2. 依存パッケージインストール

   ```bash
   npm install
   ```
3. `src/config.ts` にAPIエンドポイント設定
4. 開発サーバ起動

   ```bash
   npm start
   ```
5. 本番ビルド

   ```bash
   npm run build
   ```

## 使い方

1. ESP32-S3を起動してWi-Fi接続
2. バックエンドのログでデータ受信を確認
3. ブラウザでフロントエンドにアクセス
4. 「除外」チェックで店舗表示を切り替え
5. 「JSONエクスポート」でデータダウンロード

## 設定項目

* **ESP32-S3 (`src/main.cpp`)**: `WIFI_SSID`, `WIFI_PASSWORD`, `BACKEND_URL`
* **バックエンド (`analysis.py`)**: ポート設定（デフォルト8080）
* **App Engine (`app.yaml`)**: プロジェクトID、サービス名等
* **フロントエンド (`src/config.ts`)**: `API_BASE_URL`

## 今後の改善予定

* 満席／空席アラートのプッシュ通知
* モバイルアプリ化 (React Native)
* AIを用いた利用予測機能
* UI/UX強化（ショートカット、複数画像表示など）

## 貢献者

* 玉城（@gusuku-oknw）
* 岡田（@otake-code）
* 田上
* 立岩

## 連絡先

ご質問は以下までご連絡ください。

* メール: [gusuku.oknw@example.com](mailto:gusuku.oknw@example.com)

---

**Enjoy monitoring!**
