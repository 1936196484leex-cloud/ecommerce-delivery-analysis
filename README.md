# 电商配送延迟分析与风险预测
## E-commerce Delivery Risk Analysis & Prediction

> 基于 9.6 万+ 电商订单，完成从 EDA、特征工程、机器学习建模、阈值优化、错误分析到业务成本情景分析的完整流程，并构建 Gradient Boosting 配送延迟风险模型。

## Project Highlights

- 96,000+ orders analyzed
- End-to-end workflow: EDA → Feature Engineering → ML → Threshold Tuning → Error Analysis
- Final model: Gradient Boosting
- ROC-AUC: ~0.73
- PR-AUC: ~0.20
- Delayed-order Precision: ~0.24
- Delayed-order Recall: ~0.31
- Delayed-order F1-score: ~0.27
- F1-optimal threshold: ~0.156
- Business-cost scenario threshold: ~0.17

### English Summary

This project analyzes e-commerce delivery performance and develops a machine learning model to identify orders at higher risk of late delivery.

Using more than 96,000 orders, the project covers exploratory data analysis, feature engineering, model comparison, class-imbalance evaluation, threshold tuning, feature importance, error analysis, and business-cost-oriented decision making.

Gradient Boosting achieved the strongest overall performance, with a final test ROC-AUC of approximately 0.73 and PR-AUC of approximately 0.20.

The model is positioned as a risk-ranking and operational decision-support tool rather than a fully automated production system.

---

## 1. 项目背景

电商平台中的配送延迟会影响客户体验、售后成本和平台运营效率。

本项目希望回答以下问题：

1. 哪些订单特征与配送延迟存在明显关联？
2. 能否利用订单信息提前识别高风险订单？
3. 哪种机器学习模型更适合当前数据？
4. 模型最容易在哪些情况下产生误报或漏报？
5. 在不同业务成本假设下，应该如何选择预测阈值？

---

## 2. 数据集

本项目使用 Olist Brazilian E-commerce Dataset，主要涉及：

- Orders
- Order Items
- Customers
- Sellers
- Products
- Payments
- Geolocation
- Reviews

最终分析及建模数据包含约 96,000+ 笔有效订单。

---

## 3. 项目流程

项目主要流程：

1. 数据读取与清洗
2. 配送表现 KPI 分析
3. 探索性数据分析
4. 特征工程
5. Logistic Regression
6. Random Forest
7. Gradient Boosting
8. 类别不平衡评估
9. Threshold Tuning
10. Train / Validation / Test
11. Feature Importance
12. Error Analysis
13. Business Cost Scenario Analysis

---

## 4. 探索性数据分析

### 4.1 商品数量

订单商品数量与配送延迟率之间未表现出明显稳定关系，因此商品数量不是当前数据中最主要的延迟风险因素。

### 4.2 运费

不同运费分组的延迟率存在明显差异：

- 最低运费组延迟率约：6.00%
- 最高运费组延迟率约：9.25%

说明较高运费订单通常伴随更高的配送延迟风险。

需要注意的是，该结果表示相关关系，而非因果关系。

### 4.3 配送距离

配送距离与延迟风险表现出更加明显的正向关系：

- 最低距离组延迟率约：6.33%
- 最高距离组延迟率约：10.36%

配送距离是后续模型中的重要预测变量之一。

### 4.4 客户地区

不同客户州之间的配送延迟率存在明显差异，说明区域物流环境和地理位置可能对配送表现产生重要影响。

### 4.5 下单月份

不同月份的配送延迟率波动明显，说明订单配送存在明显的时间和季节性特征。

---

## 5. 特征工程

主要建模特征包括：

- `freight_value`
- `item_count`
- `max_distance_km`
- `order_month`
- customer state One-Hot Encoding

其中 `max_distance_km` 通过卖家与客户的经纬度计算得到。

对于包含多个卖家的订单，使用最长配送距离作为订单层面的主要距离指标。

---

## 6. 模型对比

本项目依次构建：

1. Logistic Regression
2. Logistic Regression + Customer State
3. Random Forest
4. Gradient Boosting

模型表现：

| 模型 | ROC-AUC | PR-AUC |
|---|---:|---:|
| Logistic Regression | 0.572 | 0.107 |
| Logistic + Customer State | 0.616 | 0.128 |
| Random Forest | 0.689 | 0.164 |
| Gradient Boosting | 0.727 | 0.200 |

Gradient Boosting 在整体区分能力上表现最好。

---

## 7. 类别不平衡

配送延迟订单约占全部订单的：

`8.1%`

因此 Accuracy 并不能作为主要模型指标。

例如，即使模型全部预测为“不延迟”，Accuracy 仍然可以接近 92%。

因此项目重点使用：

- Precision
- Recall
- F1-score
- ROC-AUC
- PR-AUC

进行模型评估。

---

## 8. 阈值优化

默认分类阈值 0.5 对当前类别不平衡问题并不适合。

通过 Validation Set 对模型阈值进行优化。

基于 F1-score 最大化得到：

- Best Threshold ≈ 0.156
- Validation Precision ≈ 0.25
- Validation Recall ≈ 0.33
- Validation F1 ≈ 0.29

随后固定该阈值，在独立 Test Set 上进行最终评估。

---

## 9. 最终模型表现

Gradient Boosting 最终测试结果约为：

- ROC-AUC：0.73
- PR-AUC：0.20
- Precision：0.24
- Recall：0.31
- F1-score：0.27

Validation 与 Test 表现较为接近，说明模型具备一定泛化能力。

---

## 10. 特征重要性

模型中较重要的特征包括：

1. `order_month`
2. `max_distance_km`
3. 客户州相关特征
4. `freight_value`

相比之下，`item_count` 的重要性较低。

结果与前期 EDA 基本一致。

需要注意：

> Feature Importance 只表示模型对变量的依赖程度，不代表变量的因果作用，也不能直接判断影响方向。

---

## 11. Error Analysis

模型错误主要分为：

- False Positive：预测延迟，但实际未延迟
- False Negative：预测正常，但实际延迟

### 11.1 距离维度

随着配送距离增加，False Positive 明显上升。

说明模型可能对长距离订单存在过度预警。

### 11.2 月份维度

部分月份的 False Positive 显著偏高，尤其在：

- 2月
- 3月
- 11月
- 12月

说明模型对月份特征依赖较强。

### 11.3 地区维度

模型在不同客户州之间表现不一致。

部分州同时出现较高的 False Positive 和 False Negative，说明这些地区可能需要更细粒度的物流特征进行描述。

---

## 12. 业务成本情景分析

模型的统计最优阈值不一定等于业务最优阈值。

由于缺乏真实企业成本数据，本项目设置一个示例情景：

`False Negative Cost = 5 × False Positive Cost`

在该假设下：

- Business Threshold ≈ 0.17

该结果与 F1 最优阈值约 0.156 较为接近。

该分析说明：

> 实际业务中的最终阈值，应由误报成本与漏报成本共同决定，而不是直接使用默认 0.5。

---

## 13. 主要业务发现

本项目得到以下核心结论：

- 配送距离是重要的延迟风险因素。
- 较高运费订单通常具有较高延迟率。
- 配送表现存在明显的地区差异。
- 配送表现存在明显的时间波动。
- 商品数量的解释能力相对较弱。
- Gradient Boosting 在候选模型中整体表现最好。
- 当前模型更适合作为风险排序工具，而不是完全自动化决策系统。
- 模型容易对部分长距离订单和特定月份产生过度预警。
- 最终业务阈值应该结合误报和漏报成本进行选择。

---

## 14. 业务应用场景

模型可以作为运营辅助工具，用于：

- 高风险订单优先监控
- 配送延迟预警
- 客服资源优先分配
- 区域物流表现分析
- 长距离订单风险识别
- 运营团队风险排序

---

## 15. 项目局限性

当前项目仍存在以下限制：

- 缺少物流承运商信息
- 缺少卖家历史履约能力
- 缺少实际运输路线信息
- 时间特征仍较粗糙
- 未加入节假日和促销事件
- 缺乏真实企业 FP / FN 成本数据
- 当前 Precision / Recall 仍不足以支持完全自动化决策

因此，当前模型更适合作为风险排序和业务辅助工具。

---

## 16. 后续计划

未来计划进一步完成：

- Dashboard
- Streamlit App
- 模型封装
- API
- AI Data Analyst Agent
- 自然语言数据查询
- 自动调用配送风险预测模型
- 模型解释与业务建议生成

---

## 17. 技术栈

- Python
- Pandas
- NumPy
- Matplotlib
- scikit-learn
- Jupyter Notebook
- Git
- GitHub
