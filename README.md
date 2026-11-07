# SpaceX Falcon 9 First Stage Landing Prediction
Predicting whether the Falcon 9 first stage lands successfully, using public launch data.

**Presentation:** [Final report slides (PDF)](presentation/spacex_falcon9_capstone_presentation.pdf)
**Course:** [IBM Data Science Professional Certificate (Coursera)](https://www.coursera.org/professional-certificates/ibm-data-science) · [My certificate](https://coursera.org/share/eb3d08d8fb96e394144651a5769aeb20) (completed Nov 2026)

## Question
SpaceX advertises Falcon 9 launches at about $62 million, while other providers charge $165 million or more. Much of the saving comes from landing and reusing the first stage.
Playing the role of a data scientist at a competing launch company, I asked: **can we predict, before launch, whether the first stage will land?** The answer helps estimate SpaceX's launch cost when bidding against it.

## Key results
- **The best models predicted 15 of 18 test flights (0.833).** Logistic Regression and SVM tied, above the 0.667 baseline of always predicting "land". All of their errors were false positives (a failed landing predicted as a landing).
- **Timing explains most of the differences.** Landing success rose from 43% (flights 1–30) to 83% (flights 61–90). When each group is compared only with flights from the same period, the gaps by launch site, payload and orbit mostly disappear (for example, KSC LC-39A 0.77 vs other sites 0.79 from flight 27 onward).
- **The real question is whether SpaceX tries to land.** Since flight 31, 47 of 50 attempted landings succeeded. Most "failures" in the data were flights with no landing attempt.

**Recommendation:** when bidding, first check whether SpaceX plans to recover the booster, then use the model as a guide.

## What I added beyond the course labs
This project is based on the capstone labs of the [IBM Data Science Professional Certificate](https://www.coursera.org/professional-certificates/ibm-data-science) (Coursera). On top of the required tasks, I checked the following myself:

- **Mixed label:** 21 of the 30 flights labeled as failures (Class = 0) made no landing attempt. GTO looks weak at 52% overall, but reaches 93% (14 of 15) when a landing was attempted. The 2018 dip has the same cause (61% overall, 92% when attempted).
- **Same-period comparisons:** compared launch sites, heavy payloads and VLEO only with flights from the same period, to separate their effect from timing.
- **Two-stage view:** split landing into "was a landing attempted?" and "did it succeed when attempted?" by period.
- **How reliable the test result is:** with 18 test flights, one flight changes accuracy by 5.6 points. A bootstrap 95% interval for 0.833 is about 0.67–1.00, which overlaps the baseline.
- **Data quality checks:** trailing spaces in labels, the same pad recorded under two names (CCAFS LC-40 and SLC-40, about 100 m apart, never used in the same period), and how `LIKE` vs `=` changes SQL totals (NASA (CRS) payload: 48,213 kg vs 45,596 kg).

## Limitations and next steps
- **Mixed label:** Class = 0 combines failed landings and flights with no landing attempt, so the model partly predicts whether SpaceX tries to land.
- **Small sample:** 90 flights in total and 18 in the test set, so model rankings differ by only 1–2 flights.
- **Overlapping factors:** Flight Number and Block are strongly correlated (0.93), so experience and booster upgrades cannot be separated.
- **Different sources:** row counts and periods differ by dataset (see below), and the data ends in 2020.
- **Next step:** a two-stage model that first predicts whether a landing will be attempted, then whether it will succeed.

## Notebooks
| # | Notebook | What it does |
|---|---|---|
| 01 | [Data collection (API)](https://nbviewer.org/github/hokuamadata/spacex-falcon9-landing-prediction/blob/main/01_data_collection_api.ipynb) | Collect launch data from the SpaceX REST API |
| 02 | [Web scraping](https://nbviewer.org/github/hokuamadata/spacex-falcon9-landing-prediction/blob/main/02_web_scraping.ipynb) | Scrape Falcon 9 launch records from Wikipedia with BeautifulSoup |
| 03 | [Data wrangling](https://nbviewer.org/github/hokuamadata/spacex-falcon9-landing-prediction/blob/main/03_data_wrangling.ipynb) | Clean the data and create the landing label (Class) |
| 04 | [EDA with SQL](https://nbviewer.org/github/hokuamadata/spacex-falcon9-landing-prediction/blob/main/04_eda_sql_sqllite.ipynb) | Summarize launch sites, payloads and outcomes with SQL |
| 05 | [EDA with visualization](https://nbviewer.org/github/hokuamadata/spacex-falcon9-landing-prediction/blob/main/05_eda_with_visualization.ipynb) | Charts, same-period comparisons and the two-stage view |
| 06 | [Interactive map (Folium)](https://nbviewer.org/github/hokuamadata/spacex-falcon9-landing-prediction/blob/main/06_interactive_map_folium.ipynb) | Map launch sites, outcomes and distances to nearby places |
| 07 | [Dashboard (Dash)](07_interactive_dashboard_dash) · [notebook](https://nbviewer.org/github/hokuamadata/spacex-falcon9-landing-prediction/blob/main/07_interactive_dashboard_dash/dashboard_check.ipynb) | Plotly Dash app with a site dropdown, pie chart and payload slider |
| 08 | [Machine learning prediction](https://nbviewer.org/github/hokuamadata/spacex-falcon9-landing-prediction/blob/main/08_machine_learning_prediction.ipynb) | Tune and compare 4 classification models, bootstrap interval |

## Data
| Source | Rows | Period | Used in |
|---|---|---|---|
| SpaceX REST API (Falcon 9 only) | 90 | Jun 2010 – Nov 2020 | Data collection, wrangling, EDA charts, ML |
| SQL table (Spacex.csv, provided by the course) | 101 | Jun 2010 – Dec 2020 | EDA with SQL |
| Folium / Dash CSV (provided by the course) | 56 | Jun 2010 – Jun 2018 | Interactive map, dashboard |

When the SpaceX API did not respond, I used the snapshot data provided by the course.

## Tools
Python, pandas, NumPy, requests, BeautifulSoup, SQL (SQLite), Matplotlib, seaborn, Folium, Plotly Dash, scikit-learn

---

## 概要（日本語）
公開されている打ち上げデータから、Falcon 9 の第1段が着陸に成功するかを予測するプロジェクトです。

**発表スライド：** [最終レポートのスライド（日本語版・PDF）](presentation/spacex_falcon9_capstone_presentation_ja.pdf)
**講座：** [IBM Data Science Professional Certificate（Coursera）](https://www.coursera.org/professional-certificates/ibm-data-science)・[認定証](https://coursera.org/share/eb3d08d8fb96e394144651a5769aeb20)（2026年11月修了）

### 問い
SpaceX は Falcon 9 の打ち上げを約6,200万ドルとしていますが、ほかの会社は1億6,500万ドル以上かかります。安さの多くは、第1段を着陸させて再利用することによるものです。
競合のロケット会社のデータサイエンティストという設定で、**打ち上げ前の情報から、第1段が着陸するかを予測できるか**を問いにしました。予測できれば、入札の際に SpaceX の打ち上げ費用を見積もる手がかりになります。

### 主な結果
- **最良のモデルは、テスト18便のうち15便を正しく予測した（0.833）。** ロジスティック回帰と SVM が同率で、毎回「着陸する」と答えるベースライン（0.667）を上回りました。外した便はすべて偽陽性（失敗した着陸を「着陸する」と予測）でした。
- **差の大部分は時期で説明できる。** 着陸の成功率は、1〜30便の43%から61〜90便の83%に上がりました。各グループを同じ時期の便とだけ比べると、発射場・ペイロード・軌道による差はほぼ消えます（例：27便目以降では KSC LC-39A 0.77、ほかの発射場 0.79）。
- **本当の問いは、SpaceX が着陸を試みるか。** 31便目以降、着陸を試みた50便のうち47便が成功しました。データ上の「失敗」の多くは、着陸を試みなかった便でした。

**提言：** 入札では、まず SpaceX がブースターを回収する予定かを確かめ、そのうえでモデルを目安に使う。

### 講座の課題に加えて、自分で確かめたこと
このプロジェクトは、[IBM Data Science Professional Certificate](https://www.coursera.org/professional-certificates/ibm-data-science)（Coursera）のキャップストーンのラボをもとにしています。必須の課題に加えて、次のことを自分で確かめました。

- **ラベルの混在：** 失敗（Class = 0）とされた30便のうち21便は、着陸を試みていませんでした。GTO は全体では52%ですが、着陸を試みた便だけなら93%（15便中14便）です。2018年の落ち込みも同じ理由でした（全体61%、試みた便だけなら92%）。
- **同じ時期での比較：** 発射場・重いペイロード・VLEO を、同じ時期の便とだけ比べ、時期の影響と切り分けました。
- **2段階の見方：** 着陸を「試みたか」と「試みた場合に成功したか」に分けて、時期ごとに集計しました。
- **テストの結果の信頼性：** テストが18便なので、1便で正解率が5.6ポイント変わります。0.833 のブートストラップの95%区間はおよそ 0.67〜1.00 で、ベースラインと重なります。
- **データの品質の確認：** ラベルの末尾のスペース、同じ発射台が2つの名前で記録されていること（CCAFS LC-40 と SLC-40。約100 m の距離で、使われた期間が重ならない）、SQL で `LIKE` と `=` のどちらを使うかで合計が変わること（NASA (CRS) のペイロード：48,213 kg と 45,596 kg）。

### データの限界と次の一手
- **ラベルの混在：** Class = 0 には着陸の失敗と、着陸を試みなかった便が混ざっているため、モデルは「SpaceX が着陸を試みるか」も一部予測しています。
- **少ない件数：** 全体で90便、テストは18便なので、モデルの順位の差は1〜2便にすぎません。
- **要因の重なり：** 飛行番号と Block の相関が強く（0.93）、経験とブースターの改良を切り分けられません。
- **出所による違い：** データセットごとに行数と期間が違い（上の表）、データは2020年まで。
- **次の一手：** まず「着陸を試みるか」を予測し、次に「成功するか」を予測する2段階のモデル。

ノートブックの一覧・データの出所・使った技術は、英語の部分の表をご確認ください。
