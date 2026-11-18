# Custom：用户要求创建的技能

这里保存用户要求总结、创建或定制的技能。完整调用与管理规则见 [上级 README.md](../README.md)，选择优先级为 `custom > common > disposable`。

## 主题与技能

| 主题目录 | 技能 | 用途 |
| --- | --- | --- |
| `agent-power/` | [bit1010-ssh](agent-power/bit1010-ssh/SKILL.md) | 根据语境区分本地 Mac 与 bit1010 Linux 实验平台，通过 mDNS 和 SSH 执行远程任务。 |
| `agent-power/` | [sparkling](agent-power/sparkling/SKILL.md) | 仅由用户明确调用的结构化业务研讨，禁止隐式启用。 |
| `robotics/` | [lste-experiment](robotics/lste-experiment/SKILL.md) | 在 bit1010 上跑 LSTE Gazebo 实验：健康检查、启停等待、成功判定与常见故障处理。 |

扫描入口为 `/Users/zrz_mac/.agents/skills/bit1010-ssh`，直接链接到 `agent-power/bit1010-ssh/`。技能实体包含 `SKILL.md` 和允许隐式调用的 `agents/openai.yaml`；分类与主题目录本身不包含 `SKILL.md`。

`lste-experiment` 的扫描入口为 `/Users/zrz_mac/.agents/skills/lste-experiment`，直接链接到 `robotics/lste-experiment/`，默认允许隐式调用。

Sparkling 扫描入口 `/Users/zrz_mac/.agents/skills/sparkling` 直接链接到 `agent-power/sparkling/`，其 `agents/openai.yaml` 设置 `allow_implicit_invocation: false`。
