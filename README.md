HackAichiで開発したリアルタイムなカフェ空席モニタリングシステムです．
ESP32S3搭載センサーが席の利用状況を検知し，Flaskバックエンド（Google App Engine上）へ送信．Reactフロントエンドで現在の空席状況や履歴を可視化します．

目次
1．機能
2．アーキテクチャ
3．リポジトリ構成
4．前提環境
5．セットアップ＆インストール
1）ESP32S3ファームウェア
2）バックエンドサーバ
3）フロントエンドアプリ
6．使い方
7．設定項目
8．今後の改善予定
9．貢献者
10．連絡先

1．機能
・リアルタイム空席表示：各店舗の現在の空席数をダッシュボードで表示
・履歴グラフ：過去24時間の利用推移をチャート表示
・店舗除外機能：メンテナンス中の店舗を一覧から除外
・データエクスポート：現在値・履歴データをJSONでダウンロード

2．アーキテクチャ
[ESP32S3センサー]
↓ HTTP POST（JSON）
[FlaskバックエンドAPI]──Google App Engine
↑ WebSocket／HTTP
[ReactフロントエンドSPA]

・ESP32S3：顔認証カメラ＋超音波センサーで入退店・席利用を検知し，定期的にJSONを送信
・バックエンド（Python＋Flask）：analysis.pyでデータ受信・集計・保存，app.yamlでGAEデプロイ設定
・フロントエンド（TypeScript＋React）：バックエンドからライブデータを取得し，ダッシュボードとグラフを描画

3．リポジトリ構成
/
├ .idea/ （IDE 設定）
├ ESP32S3/ （センサー用ファームウェア）
│ ├ platformio.ini
│ └ src/
│ └ main.cpp
├ BackEnd/ （Flaskバックエンド）
│ ├ analysis.py
│ ├ requirements.txt
│ └ app.yaml
├ frontend/ （React SPA）
│ ├ public/
│ └ src/
├ .gitignore
└ README.txt

4．前提環境
・ESP32S3開発環境（Arduino IDE または PlatformIO）
・Python 3.8以上
・Node.js 14以上＆npm
・Google Cloud SDK（App Engineデプロイ用）

5．セットアップ＆インストール
1）ESP32S3ファームウェア
cd ESP32S3
pio run --target upload
（事前にsrc/main.cppでWi-Fi設定とバックエンドURLを編集）

2）バックエンドサーバ
cd BackEnd
python -m venv venv
source venv/bin/activate
pip install -r requirements.txt
（ローカル起動）
python analysis.py
（GAEデプロイ）
gcloud app deploy app.yaml
※gcloud config set project YOUR_PROJECT_ID

3）フロントエンドアプリ
cd frontend
npm install
npm run build （本番ビルド）
npm start （開発サーバ: http://localhost:3000）
（必要に応じてsrc/config.tsでAPIエンドポイントを設定）

6．使い方
1．ESP32S3を起動し，Wi-Fiに接続
2．バックエンドログでJSON受信を確認
3．ブラウザでReactアプリを開き，空席状況と履歴を確認
4．「除外」チェックで非表示にする店舗を設定
5．「JSONエクスポート」で最新データと履歴をダウンロード

7．設定項目
・ESP32S3：main.cpp内のWIFI_SSID，WIFI_PASSWORD，BACKEND_URL
・バックエンド：analysis.pyのポート（デフォルト8080），app.yamlのGAE設定
・フロントエンド：src/config.tsのAPIベースURL

8．今後の改善予定
・満席／空席アラートのプッシュ通知
・モバイルアプリ化（React Native）
・AIによる利用予測機能
・UIのショートカット＆マルチ画像表示

9．貢献者
・玉城（@gusuku-oknw）
・岡田 (@otake-code)
・田上（)
・立岩（)

10．連絡先
ご質問やご意見は以下までお問い合わせください．
gusuku.oknw@example.com
