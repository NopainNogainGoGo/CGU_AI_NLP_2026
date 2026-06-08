# CGU_AI_NLP_2026
大四下修的課，內容為自然語言處理 (NLP) 與大型語言模型 (LLM)相關的技術

整個課程規劃共 16 週，從最基礎的傳統 NLP 概念一路銜接至當前最新的 LLM 技術與架構，課程包含:
四次作業和一次期末 project(Kaggle competition)

* 第 1~4 週： NLP 基礎
自然語言處理簡介、文字向量與詞嵌入、經典的循環神經網路 (RNN) 與 LSTM，以及現代 NLP 的重要技術——注意力機制 (Attention Mechanism) 與 BPE 分詞演算法。
* 第 5~6 週：主流經典模型架構
介紹 Transformer 模型架構，並聚焦於以 BERT 為代表的 Encoder-based 模型與其問答應用 (QA)。
* 第 8~10 週：解碼器與實務工具上手
介紹 GPT 系列所屬的 Decoder-based 模型。第 9 週為期中考，第 10 週則著重於 HuggingFace 實作教學（包含 gpt2 程式碼練習）。
* 第 11~14 週：大型語言模型 (LLM) 專題
這是課程的重頭戲，探討現代 LLM 的核心技術：
LLM-1：LLM 導論與檢索增強生成 (RAG)。
LLM-2：模型評估 (Evaluations) 與 Qwen3 實作。
LLM-3：高效訓練技術，包含高效參數微調 (PEFT / LoRA)。

* 第 15~16 週：專案實作成果發表(我自己一個人一組)
課程最後兩週為小組實作成果報告，讓我們將前面所學的理論與程式技能，應用到真實的專案開發中。
我的題目是Learning Agency Lab - Automated Essay Scoring 2.0 (簡稱 AES 2.0) 是 Kaggle 於 2024 年舉辦的一場自然語言處理（NLP）競賽。這項比賽的核心目標是透過機器學習與深度學習模型，自動批改學生撰寫的論說文，以減輕教師的批改負擔，並提供即時反饋。

## 成果
以Learning Agency Lab – Automated Essay Scoring 2.0 (AES 2.0)為題 ，從資料特徵、QWK 評估指標，一路上克服了標籤落差、資料不平衡等難關 。最終成功在 Kaggle 平台上拿下了 **Private Score 0.7918 / Public Score 0.7818** 的成績

## 1. 優化策略
*
捨棄傳統分類，改採回歸任務（Regression）： 若將 1~6 分當作傳統多元分類，模型會失去類別間的「距離感」與「順序感」。因此我採用均方誤差（MSE）作為損失函數（Loss Function），讓模型輸出連續的浮點數（例如 3.42 分），使模型的訓練邏輯與比賽指標 QWK（二次加權 Kappa）高度契合 。
*
動態閾值優化（Threshold Optimization）：針對回歸輸出的連續浮點數，捨棄了四捨五入，引入Nelder-Mead 演算法在驗證集上反覆尋找完美的切分點 ，最終成功得出更高的 QWK 分數 。

## 2. Pseudo-Labeling & Label Alignment
*
找出 Domain Shift 問題：我發現直接將學術語料庫 PERSUADE 2.0（2.5 萬筆）與AES 2.0 競賽資料（1.7 萬筆）混合做兩階段訓練效果不佳，觀察到新舊資料存在評分標準不一的「標籤落差」。
*
實作偽標籤對齊：先用標籤正確的 AES 2.0 新資料訓練一個 DeBERTa 模型，回頭去預測 PERSUADE 2.0 舊資料，輸出浮點數偽標籤（如 3.42、4.78 分）。
* 
最後將「新資料」與「重新預測的浮點數舊資料」打包混合訓練，成功增強了模型的語意理解與泛化能力 。


## 3. 實作成果與數據
* 
**核心模型架構：** 基於 `deberta_v3_base`（Sequence Length = 512，Batch Size = 32）進行微調 。
* 
**最終預測成績：** **Private Score: 0.7918 / Public Score: 0.7818**（平均 CV QWK 達 0.7940） 。


老師的課程連結:
https://github.com/mcps5601/CGUNLP_2026_Spring
