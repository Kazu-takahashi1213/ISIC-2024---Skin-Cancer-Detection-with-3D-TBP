![Joseph KZ - ISIC 2024 - Skin Cancer Detection with 3D-TBP](https://github.com/user-attachments/assets/aca05b7d-5998-4e4f-9236-e0301f97afd1)

ISIC2024で使用したモデルの概要とその流れ

～概要～

ISIC 2024 Skin Cancer Classification Challengeにおいて、皮膚がん病変の悪性／良性判定を高精度で実現するための構造化データベース分類パイプラインを構築。3Dテクスチャベースパターン（TBP）特徴やImageNet由来の外部予測スコアを活用し、LightGBM・CatBoost・XGBoostをアンサンブルしたVotingClassifierによりモデル性能を最大化した。さらに、患者単位での層別交差検証、外部特徴への微小ノイズ注入（いわゆる“Magic Noise”）といった工夫を取り入れ、汎化性能とロバスト性を両立する構成を目指した。最終的にROC AUCを主要評価指標とし、再現性と信頼性を確保した分類モデルを完成させた。

～当モデルの目的・流れ～

〇 目的

皮膚病変画像と構造化情報をもとに、「悪性か良性か」を予測する2値分類モデルを構築。ISIC 2024 Challengeの評価基準（主にAUC）において高スコアを目指す。

〇 データ概要

構造化メタデータ（CSV）： 患者ID、年齢、病変サイズ、病変タイプなど

TBP特徴量（3Dテクスチャパターン）： LAB色空間における明度差・色差・面積・形状などを定量化した指標群

外部予測スコア（imagenet_predict）： ImageNet事前学習モデルによる0–1の予測スコア

〇 手順

① 特徴量エンジニアリング

・20種類以上の数値型特徴量（num_cols）を選定

・TBP由来の色差（ΔL, ΔB）・周辺皮膚とのコントラスト、面積、eccentricityなどを活用

・欠損値補完（主に平均補完や0埋め）

・ImageNet予測値に微小なガウスノイズを加えて過学習を抑制


②　モデル構築：アンサンブル

LightGBM, XGBoost, CAtboostの3モデルをVotingClassifierで統合（ソフト投票）

・各重みは0.5, 0.45, 0.1に設定


③ 交差検証とサンプリング

StratifiedGroupKFold（5分割）を用いて、患者単位でグルーピングして情報リークを防止

・不均衡データ対策として、オーバーサンプリング（SMOTEなど）やアンダーサンプリングを併用


④ Magic Noise（ノイズ注入）

外部特徴（ImageNet予測スコア）に対して、極小のガウスノイズ（例：0.0000003）を加える

・モデルの出力多様性を上げ、過学習の抑制と汎化性能の向上


⑤ 評価指標と最終予測

評価指標は ROC AUC

・各FoldのAUCを平均してモデル性能を評価

・最終モデルは全データで再学習し、テストデータに対して predict_proba() により予測確率を出力

・submission.csv にフォーマットしてKaggle提出可能
