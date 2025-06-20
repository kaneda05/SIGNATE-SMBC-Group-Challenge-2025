![comp](./data/info/images/001_comp.png)

# SIGNATE-SMBC-Group-Challenge-2025
SIGNATE-SMBC-Group-Challenge-2025　コンペのリポジトリ

- directory tree
```
SIGNATE-SMBC-Group-Challenge-2025
├── README.md
├── data         <---- gitで管理するデータ
├── data_ignore  <---- .gitignoreに記述されているディレクトリ(モデルとか、特徴量とか、データセットとか)
├── nb           <---- jupyter notebook で作業したノートブック
├── nb_download  <---- 参考にしたノートブック
```

## Dataset
|Name|Detail|Ref|
|---|---|---|
|name|detail|[xxx](xxx)|

## Event
|Name|Detail|Ref|
|---|---|---|
|name|detail|[xxx](xxx)|

## Features
|Name|shape (feat only)|size(MB)|Detail|
|---|---|---|---|
|xxx.csv|(21,375, 11)|2.0|xxx|

## Paper
|No.|Status|Name|Detail|Date|Url|
|---|---|---|---|---|---|
|01|<font color='gray'>Done/Doing/Todo</font>|Name|Detail|year|[url](xx)|


## Basics
**Overview**
本コンペティションでは、皆さんのデータサイエンススキルを駆使し、変動するスペインの電力価格を予測する高精度なモデルを構築していただきます。
私たちが目指すのは、可能な限り現実の業務に近い環境で、皆さんの分析力と問題解決能力を試していただくことです。
実際のスペインにおける最終的な電力価格を予測するプロセスは、大まかに以下の段階を経ると考えられます。

1.  翌日の気象を予測する（気象予報）
2. 「①気象予報」と過去の需給実績などから、翌日の電力の需要量・供給量を予測する
3. 「②需給予測」などに基づき、電力価格を予測する
4.  その後、複数の調整プロセスを経た後の、最終的な電力価格（本コンペティションの予測対象）を予測する

ただし、「①翌日の気象予測」は、多くの専門情報が必要となることや、気象予報自体の不確実性が非常に大きいことから、今回の予測タスクからは除外し、その代わりに、予測対象時刻の気象データについては「実績値」を提供しております。（電力需給情報に関しては各タイムスタンプの24時間前の電力需給実績を提供しています。） これにより、「もし気象がこのように推移すると確定していたら」という前提のもとで、「②電力需給量の予測」から「④最終的な電力価格の予測」に至るプロセス――すなわち、電力市場における価格形成メカニズムの分析と予測に、気象予報の不確かさに影響されることなく集中していただけると考えています。

### train.csv colomn infomaiton

|name|Explanation|
|----|----|
|時刻 (time)|データが記録された日時。|
|発電実績データ (generation ...)|時刻から24時間前のバイオマス、各種化石燃料（褐炭、天然ガス、石炭、石油など）、原子力、水力（揚水消費、自流式、貯水池式）、太陽光、風力（陸上・洋上）といった多様な電源別の詳細な発電実績量。|
|総電力需要実績 (total load actual)|時刻から24時間前の実際の電力需要量。|
|電力価格実績 (price actual：目的変数)|時刻と同時刻の実際の電力価格（目的変数）。電力価格は、主に需要と供給のバランスによって決定される。（詳細は「データ」タブをご参照ください。）|
|気象データ|時刻と同時刻のスペインの主要5都市（バレンシア、マドリード、ビルバオ、バルセロナ、セビリア）における詳細な気象情報。
具体的には、気温（現在・最低・最高）、気圧、湿度、風速、風向、降雨量（過去1時間・3時間）、降雪量（過去3時間）、雲量、および天候を示す各種記述データなどが含まれます。
|


## Log
### 20250618
- join
- 残り12日の状態で参加。大分遅れをとっている。。。
  - データコンペで最後まで走り抜けた記憶が無いため、日記をつけながらなんとか感想したい。
  - 参考：[Kaggle日記という戦い方](https://zenn.dev/fkubota/articles/3d8afb0e919b555ef068)
  - 参考：[github](https://github.com/fkubota/kaggle-Cornell-Birdcall-Identification)

#### 20250619
- 良い感じのEDAがまとめられていたので、よく見ておいた方が良さそう[Url](https://github.com/kaneda05/SIGNATE-SMBC-Group-Challenge-2025/blob/main/nb/01_EDA.ipynb)

ChatGPTに電力価格予測における注目すべきポイントをまとめてもらうと以下の通り。

1. 📈 時系列性の強さ
電力価格は**時間によるパターン（季節性・周期性・トレンド）**が強いです。

- 日内パターン（昼と夜の需要差）

- 曜日パターン（平日と休日の違い）

- 季節パターン（夏と冬にピーク）

→ モデルに時間特徴量（例：hour, weekday, month）を入れるのが重要です。

2. 🧊 天候要因
電力需要（=価格）には気温・湿度・日射量などが大きく影響。

例：暑い日は冷房需要で価格が上がる

→ 外部変数として**気象データ（例：気温・湿度）**を組み込むと精度が向上します。

3. 🔄 需要と供給のバランス
再生可能エネルギー（太陽光・風力）の出力予測も重要。

これらは不安定で突発的な供給量変動を引き起こす

→ 発電量・需要予測・再エネ比率などがあればモデルに使える

4. 💥 異常値とスパイク
電力価格には**突発的な高騰（スパイク）**があり得ます。

発電所トラブル、天災、需給ひっ迫など

→ 外れ値処理 or スパイクの検知と補正を意識する必要あり

5. ⏰ 遅れ変数（ラグ特徴量）
過去の価格（例：1時間前、24時間前、1週間前など）は予測に重要。

→ ラグ変数、移動平均などの特徴量を活用（時系列モデルで定番）

6. 🔢 価格の分布特性
電力価格は非対称・尖度が高い（スパイク）分布になりやすい。

→ ログ変換や正規性を意識した前処理が必要な場合あり

使えるモデリング手法

| 分類       | モデル候補             | 特徴                  |
| -------- | ----------------- | ------------------- |
| 古典的時系列   | ARIMA, SARIMA     | シンプルだが外部変数に弱い       |
| 時系列＋外的要因 | SARIMAX, Prophet  | 天気や休日などの影響を加味可能     |
| 機械学習     | XGBoost, LightGBM | 特徴量エンジニアリングが重要      |
| 深層学習     | LSTM, TCN         | 時系列依存性を深く学習できるが計算量大 |
| 統計的手法    | ベイズ予測、状態空間モデル     | 不確実性を伴う予測が可能（MCMC等） |

| 観点     | 内容                   |
| ------ | -------------------- |
| 時間依存   | 時系列性、周期性、季節性         |
| 外的要因   | 気象、休日、再エネ、需要量        |
| 異常処理   | 外れ値、スパイク、データの揺れ      |
| 特徴量工夫  | 時間特徴、ラグ特徴、平滑化、外部変数   |
| モデル選定  | 回帰、時系列、深層学習、ベイズ等     |
| 評価の適切さ | MAE/RMSE/MAPEで実用性を測る |


言ってることはほぼ間違っていないと思うので、EDAのデータを確かめつつ、上記の視点を押さえながら
今週を目処に特徴量をいくつかピックしていきたい。