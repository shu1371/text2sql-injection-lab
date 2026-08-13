# Text-to-SQL 注入实验（Prompt-to-SQL Injection Lab）

本地可复现的 **Text-to-SQL 注入攻击与防御**实验环境：用户用自然语言提问，LLM 生成 SQL 并直接执行——本文实验展示这条链如何被“直接注入”和“数据投毒”打穿，以及三层防御（语义层 / 校验层 / 执行层）的实际效果。

对应技术文章：[文章全文（article.md）](article.md)

## 实验结果（qwen2.5:7b，每场景 8 轮）

| 场景 | 生成 SQL 涉及 users 表 | 越权查询实际执行 | 被校验器拦截 |
|---|---|---|---|
| 基线（正常问题） | 0/8 | 0/8 | 0 |
| 直接注入（恶意问题） | 7/8 | 5/8 | 0 |
| 数据投毒（规则+正常问题） | 8/8 | 3/8 | 0 |
| 语义层防御 | 2/8 | 1/8 | 0 |
| 执行层防御（SQL 校验器） | 7/8 | 0/8 | 7/8 |

## 目录结构

```text
.
├── article.md            # 技术文章全文
├── images/               # 示意图（SVG）
├── lab/
│   ├── text2sql.py       # Schema / 提示词 / LLM 生成 / SQLite 执行 / SQL 校验器
│   ├── experiments.py    # 实验编排：基线 / 注入 / 投毒 / 防御
│   └── README.md         # 实验说明
└── LICENSE               # MIT
```

## 环境要求

- Python 3.8+（**零第三方依赖**，仅标准库 `sqlite3` / `urllib`）
- [Ollama](https://ollama.com) 0.32+ 与 `qwen2.5:7b` 模型

## 快速开始

```powershell
# 1) 启动 Ollama 并拉取模型
ollama serve
ollama pull qwen2.5:7b

# 2) 进入实验目录，初始化数据库
cd lab
python experiments.py init

# 3) 依次运行实验（每场景 8 轮）
python experiments.py baseline 8
python experiments.py attack_direct 8
python experiments.py attack_data 8
python experiments.py defense_semantic 8
python experiments.py defense_exec 8

# 统计结果输出到 lab/outputs/all_stats.json
```

## 实验场景

- `baseline`：正常问题（天气 / 商品查询），验证基线行为。
- `attack_direct`：用户问题中携带“审计任务”指令，诱导生成访问 `users` 表的 UNION 查询。
- `attack_data`：数据字典/业务规则投毒——系统自动加载的“业务规则”里藏着越权指令，受害者正常提问即触发。
- `defense_semantic`：语义层防御——系统提示声明敏感表边界，禁止低可信上下文覆盖安全规则。
- `defense_exec`：执行层防御——SQL 校验器拦截非 SELECT / 多语句 / 敏感表访问。

## 免责声明

本项目全部数据为本地虚构数据，仅用于 AI 安全研究与防御验证，请勿用于未经授权的系统。
