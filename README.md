# ShopAssist
### A Retrieval-Grounded AI Agent with Evidence Verification and Human Handoff for E-Commerce Customer Service

**具備來源依據檢查與人工轉接機制的電商購物客服 AI Agent**

**Project Type:** AI Agent / Retrieval-Augmented Generation / E-Commerce Customer Service

**Status:** Research Proposal & Experimental Design

---

## 01. Project Overview｜專題概述

ShopAssist 是一套以 **Retrieval-Augmented Generation (RAG)** 為基礎的電商客服 AI Agent，主要目標是建立具備可靠性、可追溯性與人工轉接能力的智慧客服系統。

不同於傳統大型語言模型（LLM）直接生成答案的方式，ShopAssist 強調：

> **Only answer when sufficient evidence exists. Clarify when information is missing. Abstain when evidence is insufficient. Handoff when human authority is required.**

系統僅依據商店知識庫提供政策資訊，並在回覆前執行來源依據檢查，以降低 Hallucination（幻覺）與錯誤政策宣稱的風險。

本專題以虛構台灣電商作為應用情境，並參考台灣《消費者保護法》建立繁體中文政策知識庫。

---

## 02. Research Motivation｜研究動機

隨著 LLM 被廣泛導入客服服務，AI 系統逐漸承擔退換貨、配送、付款及商品相關問題。

然而，AI 客服面臨三項主要挑戰：

### 2.1 Hallucination and Incorrect Policies

LLM 可能生成知識庫不存在的政策資訊，造成消費者誤解，甚至衍生企業責任。

例如 Air Canada 客服機器人曾提供錯誤退款資訊，最終產生法律責任。

### 2.2 Limitations of Conventional RAG

RAG 雖然能透過外部文件增加回答依據，卻無法完全避免錯誤。

現有研究指出：

- LLM-only 在 CRAG Benchmark 的準確率不超過 34%。
- 直接導入 RAG 後，準確率約為 44%。
- 引用文件存在，不代表生成內容受到完整支持。
- 當檢索證據不足時，LLM 仍可能產生看似合理的答案。

### 2.3 Lack of Explicit Decision Control

傳統客服 AI 通常著重回答準確率，卻較少在同一套系統中整合以下四種處置：

1. ANSWERED — 正常回答
2. ABSTAIN — 拒絕提供無依據答案
3. CLARIFY — 要求補充必要資訊
4. HANDOFF — 轉接真人客服

因此，本研究希望整合 **Evidence Verification、Deterministic Decision Control 與 Human Handoff**，建立更加可靠的電商客服系統。

---

## 03. Research Objectives｜研究目標

本研究的核心目標如下：

**Objective 1 — Grounded Response Generation**

確保系統回答來自可追溯的商店知識庫。

**Objective 2 — Hallucination Mitigation**

透過檢索相關性、證據充分性與引用檢查，降低無依據回答。

**Objective 3 — Deterministic Action Selection**

由程式規則決定系統處置，而不是讓 LLM 自行決定所有行為。

**Objective 4 — Human Handoff Integration**

當問題涉及個別訂單、付款爭議、退款權限或顧客要求真人時，建立模擬工單。

**Objective 5 — Reliability Evaluation**

比較 LLM-only、Basic RAG 與完整 ShopAssist 系統的可靠性及效能。

---

## 04. Core Innovations｜核心創新

### Innovation 1 — Programmatic State Control

系統不完全依賴 LLM 判斷應採取的處置，而是透過規則與證據驗證結果決定最終狀態。

### Innovation 2 — Multi-Stage Evidence Verification

採用多階段證據檢查：

1. Retrieval Relevance Check
2. Evidence Sufficiency Check
3. Citation and Numerical Consistency Verification

藉此降低不具依據的政策回答。

### Innovation 3 — Four-Behavior Customer Service Agent

將 ANSWERED、ABSTAIN、CLARIFY、HANDOFF 視為四種明確、可評估的系統行為。

### Innovation 4 — Domain-Aware Policy Knowledge

將台灣電商退換貨法規、商店政策及業務權限整合到知識庫與決策流程。

**本專題的重點不是發明新的 RAG 演算法，而是整合證據檢查、行為控制與人工轉接，並評估其可靠性。**

---

## 05. System Architecture｜系統架構

ShopAssist 的整體流程：

```text
                 Customer Query
                        |
                        v
              Input Preprocessing
                        |
                        v
          Intent Classification Rules
                        |
          +-------------+-------------+
          |             |             |
          v             v             v
       Sensitive      Clarify       Handoff
       Handling       Request       Human
          |
          v
      Retrieval
          |
          v
     BM25 + Dense
       Embedding
          |
          v
 Reciprocal Rank Fusion
          |
          v
  Evidence Relevance Gate
          |
          v
      LLM Generation
          |
          v
 Evidence Sufficiency Gate
          |
          v
 Citation & Number Checking
          |
          v
   Programmatic Decision
          |
    +-----+-------+-------+
    |     |       |       |
 Answer Abstain Clarify Handoff
```

### Main Components

| Component | Function |
|---|---|
| Query Processing | 接收問題、識別意圖、遮蔽敏感資訊 |
| Planner & Reasoning Agent | 協調系統處理流程 |
| Data Retrieval | 使用 BM25 與 Dense Retrieval 搜尋知識庫 |
| Analytics Engine | 判斷檢索及證據是否充分 |
| Domain Knowledge | 提供商店政策與相關法規知識 |
| Decision Module | 以程式邏輯選擇處置 |
| Output & Interaction | 回覆結果或建立模擬轉接工單 |

---

## 06. Response Behaviors｜四種回應行為

| State | Condition | Action |
|---|---|---|
| ANSWERED | 知識庫有充分證據 | 提供有來源依據的回答 |
| ABSTAIN | 缺乏可靠證據 | 明確說明無法確認，不推測 |
| CLARIFY | 缺少必要使用者資訊 | 請使用者補充資訊 |
| HANDOFF | 需要訂單、付款或退款權限 | 建立模擬工單並轉接真人 |

### Example Scenarios

**ANSWERED**

User:「退貨期限是幾天？」

系統根據知識庫提供相關退貨規則與來源。

**ABSTAIN**

User:「退款幾天會出現在我的信用卡帳單？」

若知識庫沒有銀行入帳時間資訊，系統不自行推測。

**CLARIFY**

User:「我買的耳機還能退嗎？」

系統詢問收貨日期、商品類別及拆封情形等必要資訊。

**HANDOFF**

User:「商品壞掉了，幫我處理退款。」

系統辨識需要個案處理權限，建立模擬工單。

---

## 07. Technical Methodology｜研究方法

### 7.1 Hybrid Retrieval

本研究整合兩種檢索方法：

- **BM25:** 關鍵字與詞彙匹配
- **Dense Retrieval:** Embedding 語意相似度搜尋

使用 Reciprocal Rank Fusion（RRF）整合檢索排名。

### 7.2 Evidence Verification

系統於答案生成前後進行證據檢查。

**Pre-Generation Verification**

判斷檢索內容與使用者問題的相關性是否足夠。

**Post-Generation Verification**

確認 LLM 回報的證據充分性、引用來源及數字內容。

當檢查不通過時，系統採取 ABSTAIN，而不直接輸出原始生成答案。

### 7.3 Human Handoff

符合轉接條件時，系統建立包含以下資訊的模擬工單：

- Handoff Reason
- Masked Customer Query
- Issue Summary
- Retrieved Supporting Evidence

系統不直接執行真實付款、退款或訂單修改操作。

---

## 08. Dataset｜資料集設計

本專題採用自行設計的繁體中文電商客服評估資料集。

### Knowledge Base

| Item | Quantity |
|---|---:|
| Policy Documents | 7 |
| Knowledge Chunks | 47 |

### Evaluation Dataset

| Category | Questions |
|---|---:|
| Answerable | 20 |
| Paraphrase | 9 |
| Multi-document | 8 |
| Insufficient Evidence | 10 |
| Out-of-Scope | 6 |
| Human Handoff | 13 |
| Sensitive Information | 5 |
| Prompt Injection | 7 |
| **Total** | **78** |

資料集用於分析不同問題類型下的處置準確性與回答可靠性。

---

## 09. Experimental Design｜實驗設計

### Baseline Comparison

| Model | Description |
|---|---|
| Baseline A | LLM-only |
| Baseline B | Basic RAG |
| Proposed Model | Full ShopAssist |

比較三種架構在相同題目及生成設定下的表現。

各版本規劃至少重複執行三次。

### Ablation Study

透過移除特定模組，分析其對系統表現的影響：

1. Hybrid Retrieval vs. BM25 vs. Dense Retrieval
2. Without Intent Rules
3. Without Retrieval Relevance Gate
4. Without Evidence Sufficiency Gate
5. Without Citation Verification

### Evaluation Metrics

| Metric | Purpose |
|---|---|
| Action Accuracy | 衡量四種處置是否正確 |
| Unsafe-Answer Rate | 衡量不應直接回答卻回答的比例 |
| Over-Refusal Rate | 衡量應回答卻未回答的比例 |
| Recall@k | 衡量檢索相關文件的能力 |
| MRR | 衡量相關文件的檢索排名 |
| Citation Validity | 衡量引用是否有效 |
| Key-Fact Support | 衡量關鍵事實的依據支持 |
| Precision / Recall | 評估拒答與轉接表現 |
| Latency | 系統回應時間 |
| Token Usage | 模型運算使用量 |

---

## 10. Research Questions｜研究問題

**RQ1:** 相較於 LLM-only，Basic RAG 是否能提升可回答題目的關鍵事實命中率？

**RQ2:** 相較於 Basic RAG，ShopAssist 的證據檢查與處置規則是否能降低 Unsafe-Answer Rate，並提高 Abstention Recall？

**RQ3:** 提高系統可靠性需要付出多少代價？特別是 Over-Refusal Rate、Latency 與 Token Usage 的變化。

**RQ4:** 不同問題類別的錯誤模式有何差異？Hybrid Retrieval、BM25 與 Dense Retrieval 對結果有何影響？

---

## 11. Expected Contributions｜預期貢獻

本專題預期提供：

1. 一套可執行的繁體中文電商客服 AI Agent。
2. 整合 Evidence Verification 與 Human Handoff 的系統流程。
3. 以台灣消保法為基礎建立的示範政策知識庫。
4. 包含 78 題與八種類別的評估資料集。
5. 可重複執行的 LLM-only、Basic RAG 與完整系統比較方法。
6. 對可靠性、拒答率及客服服務範圍之間取捨的量化分析。

上述為專題規劃與預期成果，實際效能須於實驗完成後驗證。

---

## 12. Project Scope and Limitations｜研究範圍與限制

本研究目前限定於：

- 繁體中文單輪客服問題
- 虛構台灣電商商店
- 預先建立的政策知識庫
- 模擬真人轉接工單
- 不連接真實訂單或付款系統

另外，引用與數字檢查只能驗證部分依據一致性，不能保證生成答案在語意上完全正確。

未來可延伸至 Multi-turn Conversation、Semantic Entailment Verification 及真實客服系統整合。

---


## 13. References｜參考資料

本專題的研究背景與設計依據包含：

- Retrieval-Augmented Generation (RAG)
- CRAG Benchmark
- Citation Grounding and Factual Support Research
- τ-bench: Agent Evaluation
- Prompt Injection Research
- Taiwan Consumer Protection Act
- E-Commerce Customer Service Studies

完整學術引用、論文資訊及法規來源請參考原始 ShopAssist Proposal 文件。

---

**Project:** ShopAssist

**Research Focus:** Reliable and Evidence-Grounded AI Customer Service

**Keywords:** AI Agent, LLM, RAG, Hybrid Retrieval, Hallucination, Evidence Verification, Abstention, Human Handoff, E-Commerce

  
