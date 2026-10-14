# Common：用户主动安装、长期保留的技能

`common/` 是常用技能库。用户要求安装的现成技能默认放在这里，由 agent 根据技能实际用途选择主题目录。目录名用于定位候选，调用时仍要核对 `SKILL.md` 的名称、描述和适用范围。

完整读取与管理规则以 [上级 README.md](../README.md) 为准，`~/.codex/AGENTS.md` 指向该文件。本页介绍分类、用途和当前技能清单。

## 快速选择目录

| 目录 | 收录范围 | 典型任务 |
| --- | --- | --- |
| `agent-power/` | 增强 agent 的执行、思考、编排与工作流构建能力 | 配置 agent 工具链、设计工作流、多角度讨论、简化编码方案 |
| `browser/` | 网页读取、信息采集、浏览器交互与登录后操作 | 打开页面、采集网页信息、点击控件、填写表单、处理动态网页 |
| `paper-writing/` | 与论文有关的研究和写作全流程 | 查论文、读论文、文献翻译、综述、研究空白和创新点、论文写作与润色 |
| `ppt-skills/` | PPT 制作相关工具、视觉素材与文档处理 | 科研图表、示意图、PowerPoint 海报、整理 Word/Excel/PDF 材料 |
| `ppt-writing/` | 已有的整套演示文稿生成、编辑与增强工作流 | 生成可编辑 PPTX、重建页面、填充模板、优化成品演示文稿 |
| `skill-management/` | 技能发现、来源核验、安装、注册及维护 | 现有技能不适用时搜索技能、检查重复、记录来源、维护扫描链接 |

## agent-power：增强 agent 和构建工作流

这里收录改善 agent 工作方式的技能，包括 agent 工具链配置、任务编排、多视角推理、编码策略和可复用工作流。以后用户主动安装此类技能时，默认归入这个目录。

| 当前技能 | 具体功能 |
| --- | --- |
| [pi-agent](agent-power/pi-agent/SKILL.md) | 使用和扩展 Pi agent，配置模型与 provider，开发技能、扩展、SDK/RPC 集成和相关编排能力。它的适用范围以 Pi 为主。 |
| [consciousness-council](agent-power/consciousness-council/SKILL.md) | 用多个思考视角讨论问题、比较方案和权衡取舍，再汇总为行动建议。 |
| [ponytail](agent-power/ponytail/SKILL.md) | 在编码任务中选择简洁方案，优先复用标准库和原生能力，减少不必要的依赖和抽象。 |

任务示例：“用不同视角评估这几个方案”“帮我配置 Pi 工作流”“把这段实现简化一下”。

## browser：网页读取、采集与交互

这里收录 agent 从网站获取信息和操作浏览器时需要的技能，包括动态网页读取、信息采集、网页导航、登录后的交互、点击和表单操作。普通公开网页检索可以直接使用现有搜索工具；需要具体网页操作时，再选择匹配的浏览器技能。

| 当前技能 | 具体功能 |
| --- | --- |
| [slidepad](browser/slidepad/SKILL.md) | 在 Slidepad 中进行交互式或需要登录的网页操作，例如读取页面、点击控件、填写表单。 |

任务示例：“从这个网站收集信息”“读取登录后的页面”“帮我填写网页表单”。

## paper-writing：所有论文相关工作

这里收录论文研究和写作全流程的技能：文献检索、全文获取、论文精读与翻译、引用管理、系统综述、研究空白分析、创新点讨论、论证结构、章节起草、学术润色、审稿与投稿材料。

分类范围涵盖这些任务，不代表当前已经安装了每种专用技能。任务需要而已有技能不能满足时，按全局规则主动搜索补充。

| 当前技能 | 具体功能 |
| --- | --- |
| [paper-lookup](paper-writing/paper-lookup/SKILL.md) | 查询学术数据库，检索论文、标识符、摘要、开放全文和引用关系，并保留检索来源。 |
| [citation-management](paper-writing/citation-management/SKILL.md) | 提取和核对文献元数据，处理 DOI/PMID、BibTeX 和参考文献格式。 |
| [literature-review](paper-writing/literature-review/SKILL.md) | 组织多数据库检索、文献筛选、证据提取和综合，用于综述及研究空白分析。 |
| [scientific-writing](paper-writing/scientific-writing/SKILL.md) | 起草、修改和审查科研稿件，管理证据对应关系、报告规范和内容一致性。 |
| [nature-writing](paper-writing/nature-writing/SKILL.md) | 根据作者提供的证据组织论文论证、起草章节和准备首次投稿材料。 |
| [nature-polishing](paper-writing/nature-polishing/SKILL.md) | 学术翻译、润色和精简已有文字，同时保留事实、术语与证据边界。 |

任务示例：“找这个方向的最新论文”“翻译并解释这篇文献”“从综述中找研究空白”“改写论文 Introduction”。

论文内容分析和写作优先查这里；论文图表、报告幻灯片和海报的制作同时查 `ppt-skills/` 与 `ppt-writing/`。

## ppt-skills：PPT 制作、视觉表达与材料处理

这里收录所有支持 PPT 制作的工具型技能，包括科研数据图表、架构和流程示意图、版式与素材、PowerPoint 海报，以及制作演示文稿时需要的文档和表格处理。现有 Word、Excel、PDF 技能保留在此目录，可用于各自适用的独立文件任务。

| 当前技能 | 具体功能 |
| --- | --- |
| [scientific-visualization](ppt-skills/scientific-visualization/SKILL.md) | 制作和检查科研数据图表，包括多面板布局、误差与不确定性表达、颜色及导出格式。 |
| [scientific-schematics](ppt-skills/scientific-schematics/SKILL.md) | 制作科研示意图，例如神经网络架构、系统图、流程图和生物通路图。 |
| [pptx-posters](ppt-skills/pptx-posters/SKILL.md) | 创建和检查可编辑的 PowerPoint 科研海报，关注尺寸、打印与布局要求。 |
| [docx](ppt-skills/docx/SKILL.md) | 创建、读取和编辑 Word 文档与模板，处理格式、批注和修订。 |
| [xlsx](ppt-skills/xlsx/SKILL.md) | 创建和编辑 Excel 表格，处理公式、格式及数据整理。 |
| [pdf](ppt-skills/pdf/SKILL.md) | 读取和提取 PDF 内容，进行合并、拆分、转换、表单处理或 OCR。 |

任务示例：“给汇报画方法流程图”“把实验结果做成图表”“做一个 PPT 科研海报”“从 PDF/Excel 整理演示材料”。

## ppt-writing：整套演示文稿工作流

这是现有的独立主题目录，保存以完整演示文稿为主要交付物的生成、编辑和增强工作流。查找 PPT 相关技能时，要同时检查 `ppt-skills/` 和此目录，不能因两个目录名称相近漏查。

| 当前技能 | 具体功能 |
| --- | --- |
| [ppt-master](ppt-writing/ppt-master/SKILL.md) | 生成可编辑 PPTX、重建页面视觉、建立可复用风格与模板工作区、填充模板、增强成品，并处理适用的旁白和动画任务。 |

任务示例：“生成一套课程 PPT”“按参考页面重建幻灯片”“填充这个 PPT 模板”“优化现有演示文稿”。

新安装的通用 PPT 工具默认归入 `ppt-skills/`；完整演示文稿工作流可归入已有的 `ppt-writing/`。

## skill-management：发现、安装和维护技能

这里保存管理技能库的能力。任务所需技能在本地找不到时，读取 `find-skills` 的说明，主动执行 `skills find`（通常为 `npx skills find <任务关键词>`）搜索，再核验说明、来源和现有同名技能。

| 当前技能 | 具体功能 |
| --- | --- |
| [find-skills](skill-management/find-skills/SKILL.md) | 搜索技能、比较候选、核验来源并指导安装。它是发现工具，不应因为已经安装就为每个任务启动联网搜索。 |
| [autoskill](skill-management/autoskill/SKILL.md) | 用户要求分析自己的重复工作流时，通过本地 Screenpipe 数据提出技能或组合方案；需要相应服务。只有用户要求创建技能时才将新生成的实体写入 `custom/`。 |

`skill-management/` 保存管理工具；任务中主动搜索得到的业务技能安装到 `../disposable/`，供以后复用。用户明确要求安装的技能默认进入 `common/` 的对应主题目录。

## 安装和创建的默认去向

以下是用户于 2026-10-12 明确的长期偏好，完整执行规则保存在 [全局规则源](../README.md)。

| 触发方式 | 默认去向 | 分类方式 |
| --- | --- | --- |
| 用户要求“安装某个 skill”或“找一个 skill 并安装” | `common/<主题>/<技能>/` | 读取名称、描述及适用范围后，由 agent 自动选择最合适的主题。 |
| 用户要求“总结并生成一个 skill”“把工作流做成 skill” | `custom/<主题>/<技能>/` 或 `custom/<技能>/` | 保存用户要求编写的技能，按需建立主题目录。 |
| Agent 为完成业务任务主动搜索、下载缺少的 skill | `disposable/<技能>/` | 持久缓存，后续任务先复用，不因任务结束删除。 |

用户明确指定目录时遵循该指定。现成技能的下载、安装和注册授权不自动授权技能执行外部操作。技能不能满足任务时，也不自动创建新技能；创建需要用户提出。

## 查找与分类原则

1. 每个新任务先检查 `custom/`，再检查 `common/`，然后复用 `disposable/`。每一分类都先找相关主题；未命中时递归回退检查整个分类。
2. `common/` 没有匹配项时继续复用已有 `disposable/` 和能满足任务的已提供技能；仍有技能缺口时主动调用 `skills find`，按来源核验流程下载安装到 `disposable/`。简单任务直接完成。
3. 根据技能的主要输出和用途分类，不凭名称或安装量归类。跨领域技能只保留一份实体，多个任务可以复用。
4. 优先复用现有主题；确需新主题时使用清晰名称，并同步更新全局规则源和本页索引。
5. 下载前检查重复，安装后保留 `INSTALL_SOURCE.md`；每项技能通过 `~/.agents/skills/<技能名>` 的平铺软链接注册。扫描链接直接指向含 `SKILL.md` 的实体目录。
6. 不在主题目录或分类目录本身放 `SKILL.md`，不在 `~/.codex/skills/` 根目录创建技能软链接。
