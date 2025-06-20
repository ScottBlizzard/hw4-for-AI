# 大语言模型中文语义理解能力横向对比实验

本项目旨在通过一系列精心设计的中文测试用例，对两个主流大语言模型——**Zhipu AI (智谱AI)** 和 **Qwen (通义千问)**——在处理复杂语义理解任务上的性能进行横向对比分析。

实验的核心目标是评估模型在以下几个方面的能力：
- **语境歧义理解**
- **结构与语义分析**
- **复杂逻辑推理**
- **多义词辨析**

## 实验环境

本次实验在 **魔搭（ModelScope）** 平台的 **Notebook (DSW)** 环境中进行。所有模型均从 ModelScope Hub 克隆至本地实例，并利用平台提供的 GPU 资源进行推理。这种方式确保了实验的可复现性和对模型底层能力的直接评估。

## 对比模型

- **模型 A:** **Zhipu AI (智谱AI)**
  - ModelScope Hub (示例): `ZhipuAI/chatglm3-6b`
- **模型 B:** **Alibaba Cloud Qwen (通义千问)**
  - ModelScope Hub (示例): `qwen/Qwen-14B-Chat`


## 测试用例及评测目标

我们设计了以下五个具有挑战性的测试用例：

| 编号 | 测试用例 | 评测目标 |
| :--- | :--- | :--- |
| **1** | **冬天/夏天穿衣:** `冬天：能穿多少穿多少` vs `夏天：能穿多少穿多少` | 测试模型对语境（季节）如何影响语义（“多少”的极端化含义）的理解。 |
| **2** | **单身狗原因:** `单身狗产生的原因有两个，一是谁都看不上，二是谁都看不上。` | 测试模型对句子结构、语气（陈述vs疑问）以及隐含主语变化的辨析能力。 |
| **3** | **嵌套逻辑:** `他知道我知道你知道他不知道吗？` | 测试模型拆解复杂嵌套逻辑链并进行准确推理的能力。 |
| **4** | **指代关系:** `明明明明明白白白喜欢他，可她就是不说。` | 测试模型在有歧义词（明明）、多个人名和代词的情况下进行准确的指代消解能力。 |
| **5** | **多义词“意思”:** 在一段复杂对话中，辨析多个“意思”的不同含义。 | 测试模型在多轮对话流中追踪上下文，并对同一个词语的多种微妙含义进行辨析的能力。 |

## 性能表现摘要

| 测评维度 | Zhipu AI (模型A) | Qwen (模型B) | 优胜者 |
| :--- | :--- | :--- | :--- |
| **语境歧义** | 准确但表层 | 深刻且全面 | **Qwen** |
| **结构与语义分析** | 解释复杂但有偏差 | 结构清晰，分析精准 | **Qwen** |
| **复杂逻辑推理** | 逻辑混乱 | 方法正确，思路清晰 | **Qwen** |
| **指代关系** | 错误 | 正确 | **Qwen** |
| **多义词辨析** | 精确，完美贴合语境 | 通用解释，未能紧扣题目 | **Zhipu AI** |

## 结论与分析

本次实验揭示了两个模型在中文语义理解方面各自的优势领域：

1.  **Qwen (通义千问) - 强大的“语言逻辑分析器”**:
    - 在处理结构复杂、逻辑要求高的**单点式难题**时表现出色。
    - 它的回答更具结构性和分析性，能够准确地拆解句子成分、理清逻辑关系，展现了强大的基础语言处理能力。
    - 在测试1至4中均表现更优，证明了其在逻辑、结构和指代分析方面的可靠性。

2.  **Zhipu AI (智谱AI) - 卓越的“对话语境理解者”**:
    - 在需要理解**连续对话流、追踪上下文变化和辨析微妙社交语境**的任务中表现惊人。
    - 在“意思”这个最复杂的对话测试中，它完美地解读了每一处词义的细微差别，展现了近乎人类的语用学理解能力。
    - 这种能力使其在开发聊天机器人、进行情感分析和处理需要人情世故的场景时具有巨大优势。

---

## 复现指南 (on ModelScope)

按照以下步骤，您可以在 ModelScope Notebook 环境中复现本次实验：

### 1. 环境设置
- 登录 [魔搭 ModelScope](https://www.modelscope.cn/)。
- 创建一个新的 Notebook 实例（DSW），并确保选择了合适的 **GPU** 规格（例如 `NVIDIA V100` 或 `A100`）。

### 2. 克隆模型
在 Notebook 的终端或代码单元格中，使用 `snapshot_download` 函数或 `git clone` 将所需的模型文件下载到您的实例中。

```python
# 示例：使用 ModelScope SDK 下载模型
from modelscope import snapshot_download

# 下载 ZhipuAI 模型
model_dir_zhipu = snapshot_download('ZhipuAI/chatglm3-6b', revision='master')

# 下载 Qwen 模型
model_dir_qwen = snapshot_download('qwen/Qwen-14B-Chat', revision='master')

print(f"ZhipuAI 模型路径: {model_dir_zhipu}")
print(f"Qwen 模型路径: {model_dir_qwen}")
```

### 3. 准备脚本与依赖
- 将本次实验的两个 Python 脚本 (`run_zhipuai.py`, `run_qwen.py`) 上传到您的 Notebook 环境中。
- **关键：** 修改脚本，确保模型加载路径指向您在上一步中下载的本地目录（例如，`model_dir_zhipu` 和 `model_dir_qwen`）。
- 在终端中安装必要的依赖库。ModelScope 环境已预装大部分库，但建议检查并补全。
  ```bash
  pip install torch transformers sentencepiece accelerate bitsandbytes -U
  # 根据您的脚本，可能还需要其他库
  ```

### 4. 运行实验
在 Notebook 的终端中，依次执行两个脚本，并将输出结果记录下来。

```bash
# 运行 Zhipu AI 模型测试
python run_zhipuai.py

# 运行 Qwen 模型测试
python run_qwen.py
```

通过以上步骤，您就可以在相同的环境下重现本次对比实验，并验证实验结果。
