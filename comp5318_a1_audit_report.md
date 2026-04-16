# COMP5318 A1 审查报告（Rice Classification）

## 审查范围
- 作业说明：`Description(1).pdf`
- 主 notebook：`comp5318-a1-2026-template(2).ipynb`
- 数据：`rice-final2(1).csv`
- 本地测试数据：`test-before.csv`
- 说明文件：`README(1).md`

---

## 一、总体结论

这份 notebook **大部分核心功能已经完成**：预处理、Part 1、Part 2、结果打印、Random Forest 的 F1 指标、动态按最后一列取 label 的写法，整体都是对的，且代码可读性总体不错。

但若按“**满分 / 零风险提交**”标准审查，目前有 **2 个必须立刻修正的硬伤**，以及若干会影响方法严谨性、discussion 深度和代码整洁度的问题。

### 必修正（提交前一定要改）
1. **文件名不符合要求**
   - 当前 notebook 文件名：`comp5318-a1-2026-template(2).ipynb`
   - PDF 要求文件名必须包含 group number，例如 `ml-project-group69.ipynb` / `ml-project-group69.pdf`
   - 这是明确的 submission requirement。

2. **最终 notebook 里仍保留了 `test-before.csv` 的完整测试部分**
   - `Cell 34-35` 明确保留了 local test section。
   - 这和作业要求“最终提交 notebook 只保留 rice dataset 的结果，不应包含 test-before dataset 的结果”冲突。
   - 更严重的是：如果助教运行 notebook 时目录里没有 `test-before.csv`，`Cell 35` 会直接报错，存在**可运行性风险**。

### 强烈建议修正（冲满分）
3. **discussion 太泛，证据不足，未真正“compare all classifiers”**
4. **parameter tuning 的搜索网格多个模型都打到了边界值，说明当前 grid 可进一步优化或至少在 discussion 中说明其局限**
5. **代码层面仍有可精简项**：重复 import、无用 import、不必要的拟合输出、local test 逻辑与作业目标理解不完全一致。
6. **方法论上建议把预处理放进 `Pipeline` 中**，虽然这个数据集上结果几乎没变，但这是更严谨的做法。

---

## 二、逐条对照 PDF 要求的完整审查

> 结论分为：
> - **PASS**：满足要求
> - **PARTIAL**：基本满足，但离满分标准还有差距
> - **FAIL**：不满足或有明显提交风险

### A. 提交与格式要求

#### A1. Notebook 中应包含 group number 和 SID，且不要写姓名
- **要求**：PDF 明确要求 notebook 中写 group name/number 和 SID，不要写姓名。
- **对应位置**：`Cell 1`
- **审查结果**：**PASS**
- **说明**：
  - `##### Group number: 69`
  - `##### Student 1 SID: 540207680`
  - `##### Student 2 SID: 530181464`
  - 未写姓名，符合匿名要求。

#### A2. 文件名必须包含 group number
- **要求**：文件名需包含 group number。
- **当前状态**：主 notebook 文件名是 `comp5318-a1-2026-template(2).ipynb`
- **审查结果**：**FAIL**
- **问题**：提交时这是显式不合规。
- **建议**：改成如 `ml-project-group69.ipynb`；导出的 PDF 也改成 `ml-project-group69.pdf`。

#### A3. 最终 notebook 只应保留 rice dataset 的结果，不应包含 test-before 的结果
- **要求**：最终提交 notebook 只包含 `rice-final2.csv` 的结果。
- **对应位置**：`Cell 34-35`
- **审查结果**：**FAIL**
- **问题**：
  - 保留了 `### Test your code` 段落和 `test-before.csv` 测试代码；
  - `Cell 35, lines 15-19` 直接依赖 `test-before.csv` 文件；
  - 这既违反提交展示要求，也增加 runnability 风险。
- **建议**：最终提交版本删除 `Cell 34-35`，或者至少默认不执行且无任何 test-before 输出。

---

### B. 数据加载、预处理与打印

#### B1. 使用给定的 rice-final2.csv 数据集
- **对应位置**：`Cell 5, lines 1-7`
- **审查结果**：**PASS**
- **说明**：使用 `pd.read_csv(..., na_values='?')` 读取数据，符合要求。

#### B2. 缺失值用 `SimpleImputer(strategy='mean')`
- **对应位置**：`Cell 6, lines 17-19`
- **审查结果**：**PASS**
- **说明**：完全符合要求。

#### B3. 用 `MinMaxScaler` 归一化到 `[0,1]`
- **对应位置**：`Cell 6, lines 21-23`
- **审查结果**：**PASS**
- **说明**：完全符合要求。

#### B4. 类别从 `class1/class2` 改为 `0/1`
- **对应位置**：`Cell 6, lines 25-34`
- **审查结果**：**PASS**
- **说明**：映射正确：`class1 -> 0`, `class2 -> 1`。

#### B5. 打印预处理后前 10 行，特征保留 4 位小数，类别为整数
- **对应位置**：`Cell 7, lines 1-12`
- **审查结果**：**PASS**
- **说明**：
  - `print_data` 格式正确；
  - `f"{feature:.4f}"` 满足 4 位小数；
  - `int(y[example_num])` 保证类别为整数。

#### B6. 代码不能硬编码 1400 和 7，要适配未知数据集
- **对应位置**：`Cell 5-7`, `Cell 18`, 各模型 cell
- **审查结果**：**PASS**
- **说明**：
  - 使用 `iloc[:, :-1]` 和 `iloc[:, -1]` 动态切分；
  - 未硬编码 feature 数和样本数；
  - 预处理函数也按动态列数工作。
- **补充核查**：我用 `test-before.csv` 这种不同维度的数据（总列数 7，其中 6 个 feature + 1 个 class）复现了同样的主流程逻辑，动态特征处理是成立的。

#### B7. 预处理是否足够严谨
- **对应位置**：`Cell 6`
- **审查结果**：**PARTIAL**
- **说明**：
  - 从“作业显式要求”来说，你的写法是合格的；
  - 但从“机器学习方法严谨性 / 满分讨论标准”来说，`SimpleImputer` 和 `MinMaxScaler` 都是在**全数据上先 fit**，再做 cross-validation / train-test split，存在 data leakage 风险；
  - 这份数据上我复算后结果几乎没变化，但**方法论上**仍建议把预处理包进 `Pipeline`。
- **结论**：不是硬性错，但若想做到非常专业，建议修。

---

### C. Part 1：无参数调优的交叉验证

#### C1. 只实现 Logistic Regression 和 Naive Bayes
- **对应位置**：`Cell 12-13`
- **审查结果**：**PASS**

#### C2. 使用 `StratifiedKFold(n_splits=10, shuffle=True, random_state=0)`
- **对应位置**：`Cell 11`
- **审查结果**：**PASS**

#### C3. 报告 average cross-validation accuracy
- **对应位置**：`Cell 15`
- **审查结果**：**PASS**
- **结果**：
  - Logistic Regression: `0.9386`
  - Naive Bayes: `0.9264`

#### C4. random_state 设置
- **对应位置**：`Cell 12`
- **审查结果**：**PASS**
- **说明**：
  - Logistic Regression 设了 `random_state=0`；
  - GaussianNB 无 `random_state` 参数，属于正常情况。

#### C5. 代码整洁度
- **对应位置**：`Cell 12-13`
- **审查结果**：**PARTIAL**
- **问题**：
  - `Cell 12, lines 21-26` 和 `Cell 13, lines 16-18` 额外 fit 了 final model，但 Part 1 并不需要；
  - Jupyter 会把最后一个 `.fit(...)` 的 estimator repr 直接输出成 `LogisticRegression(...)`、`GaussianNB()`，造成非必要输出噪音。
- **建议**：删除这些 final fit，或将其改成赋值且不要作为 cell 最后一行输出。

---

### D. Part 2：带参数调优的分类器

#### D1. 先做 `train_test_split`，要求 stratification + random_state=0
- **对应位置**：`Cell 18, lines 6-12`
- **审查结果**：**PASS**

#### D2. 使用 GridSearchCV + 10-fold stratified CV
- **对应位置**：`Cell 20, 22, 24, 26, 28, 30`
- **审查结果**：**PASS**
- **说明**：都复用了 `cvKFold`。

#### D3. 实现了 KNN / Decision Tree / AdaBoost / Gradient Boosting / Random Forest / SVM
- **对应位置**：`Cell 20, 22, 24, 26, 28, 30`
- **审查结果**：**PASS**

#### D4. 所有可设 random_state 的分类器都设为 0
- **审查结果**：**PASS**
- **说明**：Decision Tree、AdaBoost、Gradient Boosting、Random Forest、Logistic Regression、SVM 都设置了 `random_state=0`。

#### D5. 报告 best parameters、best CV accuracy、test accuracy
- **对应位置**：`Cell 32`
- **审查结果**：**PASS**

#### D6. Random Forest 额外报告 macro F1 和 weighted F1
- **对应位置**：`Cell 28, lines 31-34`; `Cell 32, lines 30-35`
- **审查结果**：**PASS**

#### D7. 结果按 `.4f` 输出
- **对应位置**：`Cell 32`
- **审查结果**：**PASS**

#### D8. parameter tuning 输入是否正确
- **审查结果**：**PARTIAL（功能正确，但仍有提升空间）**
- **说明**：
  - 你当前所有 grid 的参数名、数据类型、模型绑定方式都是**正确的**；
  - `GradientBoostingClassifier` 直接调 `max_depth` 在 sklearn 中是合法的；
  - `RandomForestClassifier` 已按要求设置 `criterion='entropy'` 和 `max_features='sqrt'`。

但从“参数搜索质量 / 满分思路”来看，当前 grid 有一个明显信号：
- **KNN 最优 `k=7`**，打到上边界；
- **SVM 最优 `C=5`**，打到上边界；
- **Decision Tree 最优 `max_depth=3`**，打到下边界；
- **AdaBoost 最优 `learning_rate=0.1`**，打到下边界；
- **Gradient Boosting 最优 `max_depth=1, n_estimators=50, learning_rate=0.1`**，三个都打到下边界；
- **Random Forest 最优 `max_leaf_nodes=6`**，打到下边界。

这说明：
- 你的 grid **不是错的**，但它很可能**没有把最优区间完整包住**；
- 至少在 discussion 里应说明“最优值频繁落在边界，说明若允许进一步搜索，可以围绕边界继续扩展网格”。

我做了几组额外复算，结论如下：
- KNN 扩展到更大的 `k` 后，训练 CV 的最佳值会轻微上升，但 test accuracy 反而下降；
- SVM 扩展 `C` 和 `gamma` 后，最优点仍停在当前配置；
- Decision Tree 扩展更浅的深度后，CV 最优值会略变，但 test accuracy 与当前结果并无实质优势。

**结论**：当前 tuning 结果整体可接受，但 discussion 不能写成“已经找到全局最优”，更准确的表述应是“在给定搜索网格内找到最优参数”。

#### D9. Part 2 的比较是否足够严谨
- **审查结果**：**PARTIAL**
- **说明**：
  - 你当前在 Part 2 比较的是：
    - training subset 上通过 GridSearch 得到的 `best_score_`
    - 同一 held-out test split 上的 test accuracy
  - 这是符合题目要求的；
  - 但在 discussion 中如果直接拿 Part 1 的 CV 结果与 Part 2 的 best CV 结果做“调参提升”的结论，要注意它们**不是完全同一个评估设置**：
    - Part 1 的 CV 是在全数据上做；
    - Part 2 的 `best_score_` 是在 train split 上做 grid search 得到。

换言之：
- 可以比较趋势；
- 但不能把这个差值写成严格的“纯调参提升量”。

---

### E. Reflection and Discussion

#### E1. 是否比较并讨论了所有 classifiers 的表现
- **对应位置**：`Cell 37`
- **审查结果**：**PARTIAL**
- **问题**：
  - 当前 discussion 只做了泛泛总结；
  - 没有逐类比较 Logistic Regression、Naive Bayes、KNN、Decision Tree、AdaBoost、Gradient Boosting、Random Forest、SVM；
  - 没有引用具体数值进行支撑。

#### E2. 是否讨论了 hyperparameter tuning 的影响
- **对应位置**：`Cell 37`
- **审查结果**：**PARTIAL**
- **问题**：
  - 只说了 tuning “improves or stabilizes performance”；
  - 没有指出提升幅度其实很有限；
  - 没有说明多个最优值在边界上的现象；
  - 没有说明复杂模型不一定明显优于简单模型。

#### E3. 是否达到高分 discussion 的深度
- **审查结果**：**FAIL（按高分标准）**
- **核心原因**：
  1. 缺少具体数字支持；
  2. 缺少“为什么这些模型表现不同”的解释；
  3. 缺少对 mild imbalance / missingness / scaling effect 的分析；
  4. 缺少对现实部署、可解释性、计算成本、数据接入/数据库适配问题的讨论；
  5. 没有指出一个很有价值的结论：
     - **Logistic Regression 在同一个 held-out split 上其实能达到 `0.9464` 的 test accuracy，略高于 Part 2 中最好的 `0.9429`。**
     - 这说明这个数据集在线性边界下就已经相当可分，复杂模型并没有显著碾压简单模型。

---

## 三、你现在这份 notebook 的“最关键改进清单”

### 1）必须改：删除 final submission 中的 `test-before.csv` 区块
- **位置**：`Cell 34-35`
- **为什么必须改**：
  - 明确违反最终提交展示要求；
  - 会增加 notebook 无法运行的风险；
  - 该段代码对 hidden test 的理解也不完全正确。

### 2）必须改：重命名提交文件
- **建议文件名**：
  - `ml-project-group69.ipynb`
  - `ml-project-group69.pdf`

### 3）强烈建议改：discussion 重写
- 要把“泛泛而谈”改成“**数据支持 + 方法解释 + 现实建议**”。

### 4）建议改：删除多余输出与重复 import
- `Cell 4`：`matplotlib.pyplot as plt` 未使用；
- `Cell 22/24/26/28/30`：重复 import `accuracy_score` 等；
- `Cell 12-13`：无用 final fit 导致输出噪音。

### 5）建议改：若追求更专业，可把预处理包进 `Pipeline`
- 这不是题目硬性错，但能让代码更严谨；
- 同时更方便以后迁移到别的数据表或数据库抽取流程。

---

## 四、从代码、算法、数据/数据库适配、现实部署四个角度给你的 discussion 修改建议

## 4.1 代码角度
1. **强调 reusable design**
   - 你的代码没有硬编码 feature 数量；
   - 用 `iloc[:, :-1]` / `iloc[:, -1]` 支持未知列数；
   - 这点很好，应写进 discussion。

2. **强调 reproducibility**
   - 你设置了 `random_state=0`；
   - 使用 stratification；
   - 这一点保证了结果可复现。

3. **指出可进一步工程化的地方**
   - 用 `Pipeline` 避免预处理泄漏；
   - 把模型训练和评估封装成统一函数，减少重复代码；
   - 把本地测试逻辑从提交 notebook 中移除，保留成单独本地脚本。

## 4.2 算法角度
1. **不要只说谁最高**，要说“为什么差异不大”
   - 所有模型整体表现都比较高；
   - 说明这个任务在当前特征下本身已经较可分。

2. **指出简单模型并不弱**
   - Logistic Regression 的 10-fold CV accuracy 为 `0.9386`；
   - 在同一 held-out split 上复算后，Logistic Regression test accuracy 可达 `0.9464`；
   - 这比 Part 2 中最优 test accuracy `0.9429` 还略高。

3. **指出 tuning 的收益是“有限提升”，不是“巨大提升”**
   - 最优 tuned CV（Gradient Boosting `0.9446`）只比 Logistic Regression 的 Part 1 CV（`0.9386`）高 `0.0060`；
   - 这意味着 preprocessing 和数据特征本身比复杂模型更关键。

4. **指出边界最优现象**
   - 多个模型的最优参数打在网格边界；
   - 说明 grid 可以继续扩展；
   - 但不能用 test set 去决定下一轮调参。

5. **解释 Random Forest 的 F1**
   - RF 的 test accuracy 为 `0.9429`；
   - macro F1 为 `0.9414`，weighted F1 为 `0.9427`；
   - 两者接近，说明在轻度类不平衡（600 vs 800）下，两类预测都相对均衡，没有明显只偏向大类。

## 4.3 数据 / 数据库适配角度
1. **从“CSV 作业”提升到“真实数据库表”视角**
   - 真实环境中数据可能来自数据库表，而不是固定 CSV；
   - 这时要考虑 schema drift（列顺序变化、列名变化、新列/缺列）、空值比例变化、数据类型异常。

2. **建议写入 discussion 的要点**
   - 训练前应验证：
     - 最后一列是否仍是 class label；
     - feature 列是否都可转为 numeric；
     - 缺失率是否显著高于训练集。
   - 若部署到数据库抽数流程：
     - 最好按列名对齐，而不是按位置对齐；
     - 应保存训练期的 imputer/scaler 参数供线上推理复用；
     - 要记录数据版本和模型版本。

3. **为什么你当前 Cell 35 的 positional alignment 不适合最终提交 discussion**
   - 它是“把另一个文件当 external test set 再对齐到 rice 特征空间”的思路；
   - 但题目真正的 hidden test 更接近“把未知数据集当同类任务重新运行整个 pipeline”；
   - 所以 final discussion 应强调“pipeline 可在未知维度数据上重新运行”，而不是“把未知数据硬对齐到 rice 模型上预测”。

## 4.4 现实部署角度
1. **如果追求可解释性和部署简单**
   - Logistic Regression / shallow Decision Tree 更合适；
   - 易解释、训练快、维护成本低。

2. **如果追求对非线性关系更鲁棒**
   - Random Forest / Gradient Boosting 更合适；
   - 但模型更复杂，解释性较弱，参数更多。

3. **现实场景建议**
   - 若数据采集稳定、特征已工程化得较好，先上线 Logistic Regression 作为 baseline 是非常合理的；
   - 若后续观察到特征交互、边界非线性更明显，再升级到 RF / GB。

---

## 五、建议你直接替换到 notebook 最后一格的英文 discussion（可直接粘贴）

```text
Across all eight classifiers, the performance is consistently strong after preprocessing, which suggests that the Rice dataset is relatively well separated in the available feature space. In Part 1, Logistic Regression achieved an average 10-fold cross-validation accuracy of 0.9386, while Gaussian Naive Bayes achieved 0.9264. In Part 2, the best cross-validation scores on the training split were 0.9375 for KNN, 0.9357 for Decision Tree, 0.9437 for AdaBoost, 0.9446 for Gradient Boosting, 0.9411 for Random Forest, and 0.9429 for SVM. On the held-out test set, Decision Tree, Gradient Boosting, and Random Forest all reached 0.9429, AdaBoost reached 0.9393, SVM reached 0.9321, and KNN reached 0.9250. These results indicate that several models perform competitively and that no single complex model dominates the task by a large margin.

The impact of hyperparameter tuning is positive but modest rather than dramatic. The strongest tuned cross-validation result, obtained by Gradient Boosting (0.9446), is only 0.0060 higher than the Logistic Regression baseline in Part 1 (0.9386). This suggests that careful preprocessing and fair evaluation are more important than model complexity alone on this dataset. A useful additional observation is that when Logistic Regression is evaluated on the same held-out train/test split, it achieves a test accuracy of 0.9464, which is slightly higher than the best Part 2 test accuracy of 0.9429. This means that a simple linear model is already highly competitive, so increased model complexity does not automatically translate into better generalization.

The tuning results also show that several best parameters occur at the edges of the search grids, such as KNN with k=7, SVM with C=5, Decision Tree with max_depth=3, AdaBoost with learning_rate=0.1, Gradient Boosting with max_depth=1, n_estimators=50, learning_rate=0.1, and Random Forest with max_leaf_nodes=6. This indicates that the current search space is reasonable but may not fully cover the optimal region. If further tuning were allowed, the grid could be expanded around these boundary values, but model selection should still be based only on cross-validation over the training data rather than on the test set. For Random Forest, the macro F1 score (0.9414) and weighted F1 score (0.9427) are very close, which suggests that performance is balanced across both classes despite the mild class imbalance in the dataset.

From a practical perspective, the current pipeline is reusable because it does not hard-code the number of features and always treats the last column as the class label. However, for a more production-ready workflow, preprocessing should ideally be placed inside a Pipeline so that imputation and scaling are fitted only on the training folds. This is especially important when data come from a database or another external source, where schema drift, missing-value patterns, and type inconsistencies may appear over time. In practice, Logistic Regression is attractive when interpretability, speed, and ease of deployment are priorities, while Random Forest or Gradient Boosting may be preferred when capturing nonlinear interactions is more important. Overall, this assignment shows that strong preprocessing and rigorous evaluation can be as important as sophisticated model choice.
```

---

## 六、给 Codex 的“准确 Prompt” + 检测方法

下面按“问题 -> Codex prompt -> 检测方法”的形式给出。

### Prompt 1：删除 final submission 中的 `test-before` 区块（必须做）

**Codex Prompt**
```text
Edit the Jupyter notebook `comp5318-a1-2026-template.ipynb` for final submission.

Requirements:
1. Remove the entire local testing section that refers to `test-before.csv`.
2. Delete the markdown cell titled `### Test your code` and the following code cell that loads and evaluates `test-before.csv`.
3. Keep all rice dataset preprocessing, Part 1, Part 2, results, and discussion cells intact.
4. The final notebook must run top-to-bottom without requiring `test-before.csv`.
5. Do not add any new outputs unrelated to the rice dataset.

Return the edited notebook only.
```

**检测方法**
1. 搜索 notebook JSON 或文本：`test-before.csv` 应为 **0 次出现**。
2. 搜索 markdown：`### Test your code` 应为 **0 次出现**。
3. 在只保留 `rice-final2.csv` 的目录中重新 Run All，notebook 应运行成功。
4. 最终输出中只应出现 rice dataset 结果。

---

### Prompt 2：重命名提交文件（必须做）

**Codex Prompt**
```text
Prepare the final submission filenames for group 69.
Rename the notebook file to `ml-project-group69.ipynb`.
Assume the exported PDF should also be named `ml-project-group69.pdf`.
Do not change notebook content except metadata paths if needed.
```

**检测方法**
1. 最终文件名检查：
   - `ml-project-group69.ipynb`
   - `ml-project-group69.pdf`
2. 文件名中不能再出现 `template`、`(2)` 或不含组号的版本。

---

### Prompt 3：清理重复 import、无用 import 和多余输出（强烈建议）

**Codex Prompt**
```text
Refactor the notebook for cleaner submission quality without changing the reported results.

Requirements:
1. Consolidate imports into the first import cell where possible.
2. Remove unused imports such as `matplotlib.pyplot as plt` if not used anywhere.
3. Remove repeated imports from later cells unless they are strictly necessary.
4. In Part 1, remove the unnecessary final model fitting lines that only produce extra estimator repr outputs.
5. Preserve all required computations and printed metrics.
6. Keep the notebook readable and concise.
```

**检测方法**
1. Run All 后，不应再出现单独的 `LogisticRegression(...)` 或 `GaussianNB()` repr 输出。
2. 搜索 notebook：`import accuracy_score`、`import pandas as pd` 等不应在多个后续 cell 重复出现。
3. Run All 后，Part 1 和 Part 2 数值结果应与原版一致。

---

### Prompt 4：把预处理放进 Pipeline（可选的专业增强项）

**Codex Prompt**
```text
Refactor the model evaluation code in the notebook to use sklearn Pipeline for imputation and scaling during model evaluation.

Requirements:
1. Keep the printed preprocessed first 10 rows section unchanged for Assignment Part 1 display.
2. For classifier evaluation, wrap `SimpleImputer(strategy="mean")`, `MinMaxScaler()`, and each classifier in a `Pipeline`.
3. Use the pipeline inside `cross_val_score` for Part 1.
4. Use the pipeline inside `GridSearchCV` for Part 2, with parameter names prefixed by `model__`.
5. Preserve the assignment-required outputs and formatting.
6. Keep `random_state=0` wherever applicable.
7. Do not change the final reported logic other than removing preprocessing leakage.
```

**检测方法**
1. 搜索 notebook：应出现 `Pipeline([...])`。
2. Part 1 / Part 2 仍能正确运行并输出同样格式的结果。
3. `GridSearchCV` 参数名应变成 `model__...` 形式。
4. 预处理 preview 区块仍能正常打印前 10 行。

---

### Prompt 5：重写 discussion，使其达到高分标准（必须做）

**Codex Prompt**
```text
Rewrite the final discussion cell of the notebook in polished academic English.

Requirements:
1. Explicitly compare all classifiers: Logistic Regression, Naive Bayes, KNN, Decision Tree, AdaBoost, Gradient Boosting, Random Forest, and SVM.
2. Use the actual reported results from the notebook.
3. Mention that performance differences are relatively small overall.
4. Discuss the impact of hyperparameter tuning carefully and state that the tuning gain is modest rather than dramatic.
5. Mention that several best hyperparameters lie on search-grid boundaries and explain what that implies.
6. Discuss the Random Forest macro F1 and weighted F1 results.
7. Add practical insights about interpretability, deployment, and robustness to nonlinear relationships.
8. Add a brief data-engineering / schema-adaptation perspective: last-column label assumption, numeric conversion, missing-value handling, and why Pipeline is preferable in a production-like workflow.
9. Keep the tone concise, formal, and evidence-based.
10. Write 3 to 4 well-structured paragraphs.

Use these result facts in the rewritten discussion:
- Part 1 CV accuracy: Logistic Regression 0.9386, Naive Bayes 0.9264
- Part 2 best CV accuracy: KNN 0.9375, Decision Tree 0.9357, AdaBoost 0.9437, Gradient Boosting 0.9446, Random Forest 0.9411, SVM 0.9429
- Part 2 test accuracy: KNN 0.9250, Decision Tree 0.9429, AdaBoost 0.9393, Gradient Boosting 0.9429, Random Forest 0.9429, SVM 0.9321
- Random Forest macro F1 0.9414, weighted F1 0.9427
- Additional same-split observation: Logistic Regression test accuracy 0.9464
```

**检测方法**
1. 最后一格 discussion 必须明确提到所有 8 个 classifier。
2. discussion 中必须出现 tuning 影响、边界最优、RF F1、部署/解释性、数据适配这几个点。
3. discussion 不应再只有空泛表述，必须含具体数值。

---

### Prompt 6：如果你想进一步优化 grid，但又不滥用 test set（可选）

**Codex Prompt**
```text
Improve the hyperparameter-tuning discussion and optionally expand the search grids around boundary-winning values, but do not use the test set to choose parameters.

Requirements:
1. Keep the assignment-required models unchanged.
2. If a best parameter is found on a grid boundary, optionally expand the grid around that boundary using only cross-validation on the training split.
3. Do not report any claim of global optimality.
4. Make it explicit that the selected parameters are optimal only within the searched grid.
5. Preserve the original assignment output format.
6. If grid expansion is implemented, update the discussion accordingly.
```

**检测方法**
1. 检查是否仍然只用 training split + CV 做 model selection。
2. 检查 discussion 中是否避免使用“globally optimal”这类过强措辞。
3. 若扩 grid，确认 test set 没有被用于选参数。

---

## 七、最终提交前的满分检查清单

### 必做
- [ ] 删除 `Cell 34-35` 的 `test-before` 区块
- [ ] 重命名 notebook 和 PDF 文件，带上 group 69
- [ ] 重写最后一个 discussion cell

### 强烈建议
- [ ] 删除重复 import / 无用 import
- [ ] 删除 Part 1 多余 fit 输出
- [ ] discussion 中加入“Logistic Regression 在同一 held-out split 上达到 0.9464”的观察
- [ ] discussion 中加入“当前 tuning 最优值很多落在边界”的说明
- [ ] discussion 中加入“数据库/数据表 schema 变化、缺失值漂移、按列名对齐、保存 scaler/imputer”的现实建议

### 可选增强
- [ ] 把模型评估流程改为 `Pipeline`
- [ ] 仅基于 training CV 在边界附近小幅扩展网格

---

## 八、最终判断（按满分标准）

### 当前版本能否作为“功能完成版”
**可以。** 核心任务都做了，而且主要算法和输出都正确。

### 当前版本能否作为“零风险、冲满分版”直接提交
**不建议。**

### 你最应该先改的 3 件事
1. **删掉 `test-before.csv` 区块**
2. **改文件名**
3. **重写 discussion，写得更具体、更有证据、更有现实视角**

如果这三件事改完，再顺手清理重复 import 和多余输出，你这份作业的完成度会明显提升。
