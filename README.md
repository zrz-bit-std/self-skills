# 个人 Skills 管理与调用规则

本文件是个人技能读取与管理逻辑的唯一完整规则来源。`~/.codex/AGENTS.md` 软链接到本文件，使 Codex 在新会话中加载这些指令。`common/README.md` 和 `~/.agents/README.md` 分别说明主题分类与扫描入口，不另设优先级。分类目录决定选择偏好，软链接负责技能发现；Codex 的扫描顺序本身不保证调用优先级。

## 目录与固定优先级

技能实体统一保存在 `/Users/zrz_mac/.codex/skills/` 下：

1. `custom/`：用户要求总结、创建或定制的技能，最高优先级；仅在用户提出创建技能时生成。
2. `common/`：用户要求安装的现成技能默认存放处；按用途归入主题目录，长期保留，包括 `find-skills`。
3. `disposable/`：在任务中通过 `find-skills` 搜索后按需安装的技能。它是可复用的本地缓存，不要因任务结束自动删除。

每个技能可以使用 `<分类>/<主题目录>/<技能目录>/SKILL.md` 结构，也兼容 `<分类>/<技能目录>/SKILL.md`。例如 `common/browser/slidepad/SKILL.md`。主题目录可以继续嵌套，但建议使用少量、清晰的层级。`.system/` 是内置技能，不参与上述分类移动。

## 长期偏好：技能安装与创建（2026-10-12）

用户明确要求记住以下默认行为；后续会话通过本文件执行：

- **用户让我安装技能**：默认安装到 `common/`。读取技能的 `name`、`description` 和适用范围后，自主选择最合适的现有主题目录。用户没有要求每次确认分类，不重复询问；明确指定目录时遵循指定。
- **用户让我总结并生成技能**：才在 `custom/` 创建技能，可按用途建立主题目录。不能因暂时找不到现成技能就自行生成一个新技能。
- **任务缺少适用技能**：先按 `custom > common > disposable` 复用本地技能；`common/` 无匹配项时继续检查已有缓存及能够满足任务的已提供技能。仍有技能缺口时，主动读取 `find-skills`，执行 `skills find` 搜索、核验并下载到 `disposable/`，随后注册和使用。普通简单任务直接完成。
- **区分主动安装与任务补齐**：用户明确说“帮我找并安装一个技能”属于用户安装请求，默认进入 `common/`；agent 为完成业务任务自行发现和下载的补充技能进入 `disposable/`。
- **自动归类**：agent 能力和工作流归 `agent-power/`；网页读取与交互归 `browser/`；论文相关任务归 `paper-writing/`；PPT 及视觉和材料工具归 `ppt-skills/`；整套演示工作流可归现有 `ppt-writing/`；技能发现和管理工具归 `skill-management/`。先复用合适主题，确需新主题时同步维护索引。

本节是持久偏好的一部分；下文中的安装流程按这些默认去向执行。

## 按主题目录查找

每次检查某个分类时，先查看其中的目录名称，按任务含义选择最相关的主题目录，再检查该目录内各技能的 `name`、`description` 和适用范围。目录名是查找线索，不是技能适用性的最终判断依据。

当前主题目录：

| 目录 | 对应任务 |
| --- | --- |
| `common/paper-writing/` | 论文检索、精读与翻译、综述、创新点与研究空白、写作润色和投稿 |
| `common/agent-power/` | Agent 工具、讨论编排与编码工作方式 |
| `common/browser/` | 网页读取与信息采集、浏览器交互、登录、点击和表单操作 |
| `common/skill-management/` | 技能发现、来源核验、安装注册、工作流技能化与维护 |
| `common/ppt-skills/` | PPT 制作相关工具、科研图表、示意图、海报与文档材料处理 |
| `common/ppt-writing/` | PPT、PowerPoint 演示文稿的生成、编辑与增强 |

新增主题目录时使用能描述用途的名称；中英文目录名都可以按任务语义匹配。有多项任务时可检查多个相关主题。

若相关主题内没有适用技能，必须递归检查该分类的其他主题、嵌套目录及直接存放的技能，避免目录名不同或分类不准确造成漏查。可用 `rg --files <分类目录> -g SKILL.md` 列出技能清单。只有确认整个分类没有适用技能时，才进入下一个分类。

始终保持 `custom > common > disposable`：不能因为 `common/` 的目录名更贴合任务，就跳过 `custom/`。三个分类均没有适用技能时，才搜索新的技能。

## 每次新任务及每次选择技能前

按以下流程读取技能。每个新任务重新从 custom 开始；同一任务继续执行时可复用已选择技能，任务范围变化或需选择其他技能时重新按此流程判断。

1. 检查 `custom/`。先按主题目录名定位候选，再读取候选 `SKILL.md` 的 `name`、`description` 与适用范围。找到适用技能后，读取完整说明并使用；候选不适用时递归检查 custom 的其他目录。整个 custom 均不适用时才继续。
2. 用相同方式检查 `common/`：相关主题优先、元数据核对、未命中再检查其他目录。适用则复用；整个 common 均不适用时才继续。
3. 用相同方式检查 `disposable/`，优先复用过去安装的技能。它是持久缓存，不要因分类名称或新任务开始就重新下载。
4. 三个分类都没有适用技能时，先复用能够满足任务的已提供内置、插件或项目技能。系统、开发者及项目规定必须使用的技能仍需使用；用户明确指定某个技能时遵循用户指定。普通问答或不需要技能的简单任务直接完成。
5. 仍无适用技能、且任务需要技能支持时，才读取 `common/skill-management/find-skills/SKILL.md`，搜索并核验来源，将合适技能安装到 `disposable/`，注册扫描软链接后读取完整技能说明，继续当前任务。

`find-skills` 是发现工具，不算普通业务任务的已有解决技能；不要仅因它位于 common 就为每个任务联网搜索。用户明确要求搜索技能时，可以直接调用它，但安装前仍检查三个分类，避免下载已有技能。

多个不同子任务可分别按此流程选择技能。相同功能优先选择 `custom > common > disposable`；同分类内选择最贴合任务的技能。目录名只用于定位，不能只凭名称直接调用。通过实体路径和扫描软链接看到相同真实路径时，视为同一个技能，不重复读取或安装。

检索时先读技能元数据，选中后再读完整说明，无需为每个任务读取所有技能的脚本、参考资料和模板。没有命中一个主题目录不等于没有已有技能，必须完成该分类的回退查找再进入下一步。

## 自动搜索与安装

用户已授权：已有技能不足时按查找流程自主搜索、核验、下载、注册并调用相关技能，不需要每次询问是否安装。用户主动要求安装时，按长期偏好默认放入 `common/` 的适当主题；agent 为完成业务任务自行搜索的补充技能放入 `disposable/`。遵循 `find-skills` 的搜索和来源核验流程，跳过默认“提供命令后等待确认”的步骤。该授权只涉及技能下载、注册和调用，不扩大技能脚本或外部操作的授权范围。

- 下载前递归检查 `custom/`、`common/` 和 `disposable/` 的现有技能及名称，包括嵌套主题，避免重复安装。
- 读取候选说明，确认用途和来源；不要仅因安装量或 star 高就安装。
- 用户要求安装的现成技能：默认目标为 `/Users/zrz_mac/.codex/skills/common/<主题>/`，由 agent 根据实际功能选择主题。明确指定路径时遵循指定。
- agent 为业务任务主动搜索到的补充技能：目标为 `/Users/zrz_mac/.codex/skills/disposable/`，保留为持久缓存。
- 用户要求总结或创建技能时才写入 `custom/`；缺少合适现成技能时说明情况并继续能够独立完成的工作，不自动创建新技能。
- GitHub 来源优先使用内置 `skill-installer` 的安装脚本，显式指定 `--dest`。用户安装请求示例：

  ```sh
  SSL_CERT_FILE=/etc/ssl/cert.pem python3 /Users/zrz_mac/.codex/skills/.system/skill-installer/scripts/install-skill-from-github.py \
    --repo <owner/repo> --path <仓库内技能路径> \
    --dest /Users/zrz_mac/.codex/skills/common/<主题>
  ```

  agent 主动补齐业务任务缺少的技能时，使用同一脚本并将 `--dest` 指定为 `/Users/zrz_mac/.codex/skills/disposable`。

- 安装后检查 `SKILL.md` 可读，为每项技能注册扫描链接。下例的实体路径根据上述默认去向填写：

  ```sh
  ln -s <技能实体的绝对目录> /Users/zrz_mac/.agents/skills/<技能名>
  ```

- 链接名已存在时先确认目标；相同实体直接复用，不覆盖其他技能或文件。不同来源的同名技能使用不同目录名和链接名保留，并按分类优先级选择。
- 在安装目录的 `INSTALL_SOURCE.md` 记录来源仓库、仓库内路径和可获取的版本或提交信息。
- 没有合适技能或安装失败时说明具体情况，继续能独立完成的工作；不无限重试或声称已安装。
- 用户后来要求把缓存技能长期保留时，可以移入 `common/` 的相应主题，并更新扫描链接和相关索引。

## 软链接布局与维护

官方用户扫描目录是 `/Users/zrz_mac/.agents/skills/`，其中每个技能目录直接链接到上述分类、主题目录中的技能实体；不要只链接主题目录。技能实体嵌套多少层，不改变扫描链接的平铺结构。`/Users/zrz_mac/.codex/skills/` 根目录只保留 `common/`、`custom/`、`disposable/`、`README.md` 和隐藏的内置 `.system/`；不要在这里添加技能软链接。扫描软链接只放在 `/Users/zrz_mac/.agents/skills/` 中，软链接不复制技能内容。

### 技能目录变更时同步维护扫描链接

凡是在任务中新增、安装、移动、重命名或删除技能，都要同时检查并更新 `/Users/zrz_mac/.agents/skills/` 下该技能对应的直接链接，让扫描入口指向技能当前所在的实体目录。新增技能时按技能名创建链接；移动或重命名时只更新确认属于该技能的链接；删除技能时只删除指向该技能实体的链接。链接已存在时先检查目标：指向同一实体就复用，指向其他实体或是普通文件时不得覆盖。链接维护属于技能变更的完成步骤，应在任务完成前核验链接目标，并同步更新相关分类 README 的布局或索引说明。

现有技能归类：

- `common/agent-power/consciousness-council/`、`common/agent-power/pi-agent/`、`common/agent-power/ponytail/`
- `common/browser/slidepad/`
- `common/paper-writing/citation-management/`、`common/paper-writing/literature-review/`、`common/paper-writing/nature-polishing/`、`common/paper-writing/nature-writing/`、`common/paper-writing/paper-lookup/`、`common/paper-writing/scientific-writing/`
- `common/ppt-skills/docx/`、`common/ppt-skills/pdf/`、`common/ppt-skills/pptx-posters/`、`common/ppt-skills/scientific-schematics/`、`common/ppt-skills/scientific-visualization/`、`common/ppt-skills/xlsx/`
- `common/ppt-writing/ppt-master/`
- `common/skill-management/autoskill/`、`common/skill-management/find-skills/`

新增主题目录时同步更新本文件与 `common/README.md` 的主题索引；新增、移动、删除技能时更新相关 README 中的布局说明。今后新增、移动或删除技能时同步维护 `/Users/zrz_mac/.agents/skills/` 中的扫描链接；只替换确认属于该技能的链接，不覆盖无关目录。不要在 `common/`、`custom/`、`disposable/` 分类目录本身添加 `SKILL.md`，分类目录和主题目录都不是技能；只有真正的技能目录才包含 `SKILL.md`。

技能变化未显示时重启 Codex。全局 AGENTS 指令通常在新会话启动时加载；已有会话可显式读取本文件后遵循这些规则。此策略是智能体的调用流程，不是修改 Codex 扫描器的硬编码优先级。

官方依据：

- [技能扫描位置与软链接支持](https://learn.chatgpt.com/docs/build-skills#where-codex-loads-local-skills)
- [全局 AGENTS.md 指令发现机制](https://learn.chatgpt.com/docs/agent-configuration/agents-md#how-codex-discovers-guidance)
