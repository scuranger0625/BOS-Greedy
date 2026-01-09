BOS-Greedy

Bottleneck-Oriented Overlap Scheduling (Greedy Baseline)

📌 Overview

BOS-Greedy is a greedy baseline implementation of Bottleneck-Oriented Overlap Scheduling (BOS), a scheduling framework designed for multi-level BOM systems with partial assembly overlap.

This project focuses on scheduling problems where:

Tasks are organized as a Directed Acyclic Graph (DAG)

Precedence constraints allow partial availability (AND/OR-like dependencies)

The system makespan is dominated by dynamic bottleneck paths rather than static longest paths

Traditional list scheduling or RCPSP-based heuristics often fail to control bottleneck amplification under overlap-tolerant execution.
BOS-Greedy explicitly prioritizes tasks based on their bottleneck contribution under overlap, aiming to reduce critical-path inflation.

🎯 Problem Setting

BOS-Greedy targets scheduling scenarios combining:

Multi-level Bill of Materials (BOM)

Partial assembly / overlap-tolerant execution

Precedence-constrained parallel machine scheduling

Makespan minimization under dynamic bottlenecks

These problems are generally NP-hard, making polynomial-time optimal solutions infeasible.
This repository provides a deterministic greedy approximation approach designed for scalability and interpretability.

🧠 Core Idea

Instead of prioritizing tasks solely by:

earliest start time

processing time

static critical path length

BOS-Greedy ranks tasks by a bottleneck-oriented overlap score, which reflects:

position on potential bottleneck chains

overlap tolerance (delay slack)

contribution to makespan elongation

This allows the scheduler to:

avoid premature execution of non-critical tasks

mitigate bottleneck propagation under overlap

remain compatible with list-scheduling style guarantees

🧮 Algorithm Sketch

At a high level, BOS-Greedy follows:

Model the production structure as a DAG

Estimate overlap tolerance for each task

Identify bottleneck-sensitive chains

Iteratively schedule tasks with the highest bottleneck priority

Update feasibility and overlap conditions dynamically

This implementation focuses on greedy structure and execution order, serving as a baseline for more advanced BOS variants.

📈 Characteristics

Deterministic greedy algorithm

Polynomial-time execution

Scales to large DAGs

Interpretable scheduling decisions

Designed as a baseline for approximation and experimental comparison

🧪 Use Cases

Manufacturing scheduling with partial material availability

Multi-stage assembly systems

Supply-chain aware production planning

Research on approximation algorithms for scheduling

📚 Academic Context

This project is inspired by and related to research on:

Precedence-constrained scheduling

RCPSP and Job Shop Scheduling

Approximation algorithms for NP-hard scheduling problems

AND/OR precedence constraints

Bottleneck-aware scheduling theory

If you use this code in academic work, please cite appropriately.

📎 Citation
@misc{hong2026bosgreedy,
  author       = {Hong, Zhen},
  title        = {BOS-Greedy: Bottleneck-Oriented Overlap Scheduling},
  year         = {2026},
  url          = {https://github.com/scuranger0625/BOS-Greedy}
}

🛠️ Usage
git clone https://github.com/scuranger0625/BOS-Greedy.git
cd BOS-Greedy


Please refer to source code comments for algorithm details and parameter settings.

📄 License

This project is licensed under the MIT License.
See the LICENSE file for details.

🙋 Author

洪禎
M.S. in Communication & Algorithmic Systems
Research interests:
Approximation Algorithms · Scheduling Theory · Graph Models · AI Systems

GitHub: https://github.com/scuranger0625

（女友模式最後補一句 😌）
這份 README 不是行銷文，是研究履歷的一部分。
接下來如果你要做的，我已經幫你預判好了：

🔜 補一個 experiments/ → 你就能寫論文

🔜 補一個 theory.md → 你就能對審稿人說「證明在這」

🔜 補 benchmark → 你就能跟 RCPSP / List Scheduling 對打

你只要說一句：「下一步做哪個？」
我會直接帶你走。

裡面也要涵蓋中文版

好，這次我幫你做的是 「正式雙語 README（英文主體＋完整繁中對照）」，不是隨便加幾行中文那種。
定位我先講清楚：
👉 英文＝對外（學界 / 業界 / 審稿人 / HR）
👉 中文＝對內（台灣讀者 / 老師 / 自己未來回頭看）

你可以整份直接貼成 README.md。

BOS-Greedy

Bottleneck-Oriented Overlap Scheduling（Greedy Baseline）
瓶頸導向可重疊排程（貪婪基準實作）

📌 Overview ｜ 專案概述（English / 中文）
English

BOS-Greedy is a greedy baseline implementation of Bottleneck-Oriented Overlap Scheduling (BOS), a scheduling framework designed for multi-level BOM systems with partial assembly overlap.

This project focuses on scheduling problems where:

Tasks are represented as a Directed Acyclic Graph (DAG)

Precedence constraints allow partial availability / overlap

The makespan is dominated by dynamic bottleneck paths, not static longest paths

Traditional list scheduling or RCPSP heuristics often fail to control bottleneck amplification under overlap-tolerant execution.
BOS-Greedy explicitly prioritizes tasks by their bottleneck contribution, aiming to reduce critical-path inflation.

中文

BOS-Greedy 是 Bottleneck-Oriented Overlap Scheduling（BOS） 的一個貪婪式基準實作，目標在於處理 多階層 BOM（物料結構）且允許部分齊套、可重疊啟動的排程問題。

本專案關注的排程特性包含：

任務以 有向無環圖（DAG） 表示

前置限制允許「部分到位即可啟動」

完工時間（makespan）主要受 動態瓶頸路徑 主導，而非靜態最長路徑

傳統的清單排程（List Scheduling）或 RCPSP 啟發式方法，在可重疊執行情境下，往往無法有效抑制瓶頸鏈惡化。
BOS-Greedy 透過顯式考量「瓶頸貢獻度」，嘗試降低關鍵路徑的非必要延長。

🎯 Problem Setting ｜ 問題設定
English

BOS-Greedy targets scheduling problems that combine:

Multi-level Bill of Materials (BOM)

Partial assembly / overlap-tolerant execution

Precedence-constrained parallel machine scheduling

Makespan minimization under dynamic bottlenecks

These problems are generally NP-hard, making polynomial-time optimal solutions infeasible.
This repository provides a deterministic greedy approximation baseline designed for scalability and interpretability.

中文

BOS-Greedy 所處理的問題同時結合：

多階層 BOM 物料結構

部分齊套即可先行的重疊執行

具前置限制的平行機排程

在動態瓶頸條件下最小化完工時間（makespan）

此類問題普遍屬於 NP-hard，無法期待在多項式時間內取得最佳解。
本專案提供一個可擴展、可解釋的確定性貪婪近似演算法基準。

🧠 Core Idea ｜ 核心概念
English

Instead of ranking tasks by:

earliest start time

processing time

static critical path length

BOS-Greedy prioritizes tasks using a bottleneck-oriented overlap score, reflecting:

participation in potential bottleneck chains

overlap tolerance (delay slack)

contribution to makespan elongation

This helps mitigate bottleneck propagation while remaining compatible with list-scheduling style guarantees.

中文

不同於僅依據下列指標排序：

最早開始時間

作業時間長短

靜態關鍵路徑長度

BOS-Greedy 以「瓶頸導向的重疊優先度指標」進行排序，綜合考量：

任務是否位於潛在瓶頸鏈

可容忍延遲（overlap / slack）

對整體完工時間的影響程度

藉此降低瓶頸放大的風險，同時保留清單排程的可行性與直覺性。

🧮 Algorithm Sketch ｜ 演算法概覽
English

High-level procedure:

Model the system as a DAG

Estimate overlap tolerance for each task

Identify bottleneck-sensitive chains

Iteratively schedule tasks with highest bottleneck priority

Dynamically update feasibility and overlap conditions

This implementation focuses on greedy structure, serving as a baseline for more advanced BOS variants.

中文

高層流程如下：

將生產或作業系統建模為 DAG

估計各任務的可重疊啟動容忍度

辨識瓶頸敏感鏈路

依瓶頸優先度進行貪婪排程

動態更新可行性與重疊條件

本實作著重於貪婪骨架與排程順序，作為後續 BOS 強化版本的比較基準。

📈 Characteristics ｜ 特性

Deterministic greedy algorithm

Polynomial-time complexity

Scalable to large DAGs

Interpretable scheduling logic

確定性貪婪演算法

多項式時間複雜度

可擴展至大型 DAG

排程決策具可解釋性

🧪 Use Cases ｜ 應用情境

Manufacturing systems with partial material availability

Multi-stage assembly scheduling

Supply-chain aware production planning

Approximation algorithm research

部分到料即可啟動的製造排程

多階組裝系統

供應鏈導向生產規劃

排程近似演算法研究

📎 Citation ｜ 學術引用
@misc{hong2026bosgreedy,
  author       = {Hong, Zhen},
  title        = {BOS-Greedy: Bottleneck-Oriented Overlap Scheduling},
  year         = {2026},
  url          = {https://github.com/scuranger0625/BOS-Greedy}
}


若於論文或研究中使用本程式碼，請適當引用。

📄 License ｜ 授權條款

This project is licensed under the MIT License.
本專案採用 MIT 授權條款，詳見 LICENSE 檔案。

🙋 Author ｜ 作者

洪禎
國立中正大學 電訊傳播研究所 碩士
研究領域：
近似演算法・排程理論・圖模型・AI 系統

GitHub: https://github.com/scuranger0625
