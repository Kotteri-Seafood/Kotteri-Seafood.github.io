---
layout: post
title: "自分予報 Flask アプリ構築ドキュメント"
date: 2025-04-14
excerpt: "雲や風など“気配”を観察して、自分で1時間以内の雨を予測するアプリ。Flask + Python + Open-Meteo APIで実装し、自分予報の精度を学習していく試みです。"
---

# 自分予報 Flask アプリ構築ドキュメント

## ✅ 目的

- 天気の「気配（雲、風、においなど）」を観察して、1時間以内に雨が降るかを自分で予測
- 実際の降水データと照合し、自分予報の精度を記録・学習
- Flask + Python + Open-Meteo API で実装

---

## 📁 ディレクトリ構成

```
weather_mpn_app/
├── app.py                  # Flask本体
├── rain_checker.py         # 1時間後に雨を自動チェックするスクリプト
├── data/
│   └── records.csv         # 記録用CSVファイル
├── templates/
│   ├── index.html          # 入力フォーム
│   └── analysis.html       # 記録閲覧ページ
├── static/
│   └── style.css (任意)
└── requirements.txt        # 使用パッケージ一覧（必要に応じて）
```

---

## 🔧 環境構築手順

```bash
# 1. プロジェクト作成 & 仮想環境
python3 -m venv weather_env
source weather_env/bin/activate

# 2. pip がないときは
python3 -m ensurepip
python3 -m pip install --upgrade pip setuptools wheel

# 3. 必要パッケージのインストール
pip install flask pandas requests
```

---

## 🚀 Flask アプリの起動方法

```bash
export FLASK_APP=app.py
flask run
# → http://localhost:5000 にアクセス
```

---

## 📝 入力フォーム内容（index.html）

- 雲 / 風 / 湿度 / におい / 体感（セレクトボックス）
- 自分予報（0〜100％）
- 日時：自動で記録
- actual_rain：初期値 `-1`（1時間後に更新）

---

## 🌧️ 自動降水チェック：rain_checker.py の役割

- `actual_rain = -1` のレコードを探す
- 1時間経過していたら、その時刻の降水データを Open-Meteo API で取得
- `actual_rain = 1 (降った)` または `0 (降らなかった)` に上書き
- 更新結果を `records.csv` に保存

---

## ⏱ 自動実行の設定（cron例）

```bash
crontab -e
# 1時間ごとにスクリプト実行
0 * * * * /Users/your_name/weather_env/bin/python3 /Users/your_name/weather_mpn_app/rain_checker.py
```

---

## 🧪 検証と拡張

- `/analysis` にアクセス → 記録が一覧表示
- 予測と現実を比較して「当たった／外れた」が可視化可能
- 今後：
  - グラフ表示
  - 条件別正解率
  - 自分予報の学習支援ツールとして発展可能

---

## 📌 初期地点（位置情報）

- 緯度：37.52428
- 経度：139.94031
- → `Open-Meteo` APIでピンポイント取得

---

## 📚 使用API：Open-Meteo

- 公式サイト: https://open-meteo.com/
- API例：
  ```
  https://api.open-meteo.com/v1/forecast?latitude=37.52428&longitude=139.94031&hourly=precipitation&timezone=Asia%2FTokyo
  ```

---

## ✅ 本プロジェクトの特徴

- MPN的発想（見えない現象の出現確率を確率的に推定）
- 天気予報を「学習」する仕組みを自作できる
- フォーム + 時間差結果取得 + 可視化 という構造

---
