# Text-to-SQL 注入实验（Prompt-to-SQL 攻击本地复现）

## 环境要求

- Python 3.8+（零第三方依赖，仅标准库）
- Ollama 0.32+ 与 `qwen2.5:7b` 模型

## 快速开始

```powershell
ollama serve
ollama pull qwen2.5:7b
python experiments.py init
python experiments.py baseline 8
python experiments.py attack_direct 8
python experiments.py attack_data 8
python experiments.py defense_semantic 8
python experiments.py defense_exec 8
```

## 输出

- `outputs/*.log`：每轮问题、生成的 SQL、执行/拦截结果
- `outputs/all_stats.json`：各场景量化统计（users_ref / executed_users / blocked_events）

## 免责声明

实验数据与业务库均为本地虚构数据，仅用于 AI 安全研究与防御验证。
