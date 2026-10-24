# TavernCardStudio 工作区约定

本工作区用来在本地编写、检查、拆分和打包 SillyTavern 角色卡，供人和 AI 共同维护。用户直接在工作区对话即可，不要求填写简报或复制开工指令。按任务加载下面的技能；详细文档作为按需参考，不一次读完。

## 三条路径

| 路径 | 起点 | 之后做什么 |
| --- | --- | --- |
| 简单卡 | `pnpm card new <名称>` | 改 `src/index.yaml` 与 `src/text/*.md`；可选加世界书。人物设定可以留在卡字段，也可以放世界书 |
| 复杂卡 | `pnpm card new <名称> --modules "worldbook,mvu,vue"` | 世界书、变量、正则、脚本、界面互相引用，改动前先读 [docs/COMPLEX-CARDS.md](docs/COMPLEX-CARDS.md) 定位内容 |
| 导入卡 | `pnpm card split <PNG或JSON文件> --name <名称>` | 先 `pnpm card diff <名称>` 确认拆分无损，再在 `src/` 上改；不从原卡重新拆分覆盖编辑 |

## 跨 agent 入口

- 以当前对话和实际卡片为起点。能自行判断的细节直接处理，只询问影响目标或重要结果的缺失信息；已有创作笔记仅作参考，不要求用户补表。
- 用户请求安装或首次运行遇到环境问题时，按 [安装环境](docs/INSTALL.md) 检查并补齐 Node.js、固定版本 pnpm 和项目依赖，再验证能启动；普通设定讨论不必先跑环境检查。
- 工作顺序：本文 → 下表技能 → 当前任务需要的 docs 页。不要一次加载全部技能和 `references/`。
- 开工前用 `pnpm card map <名称>` 和卡根 `内容导航.md` 定位内容落在哪个文件，再动手改。
- 导入卡的世界书要整理完才算交付：`card map` 会给出世界书清单与待分类条目，分批读取必要正文后把目录写进条目 YAML 的 `分类`，再跑 `pnpm card organize <名称>`；范围与边界见 [docs/WORLDBOOK-FILES.md](docs/WORLDBOOK-FILES.md)。
- 改完跑 `pnpm card check <名称>` 与 `pnpm card build <名称>`；改过引用、模块或构建项再加 `pnpm card diff <名称>`。报告时把静态检查、构建和真实宿主验证分开写。

## 按任务加载技能

技能放在仓库 `.agents/skills/`，随仓库维护，不必安装到用户全局目录。支持该目录的 agent 可按描述选择；其它 AI 按下表直接读取 `SKILL.md`，同样能使用。上游参考目录中的技能属于研究资料，不是本仓库执行规范。

| 任务 | 入口 |
| --- | --- |
| 新卡、整体改造、复杂卡研究 | [.agents/skills/tavern-card-authoring/SKILL.md](.agents/skills/tavern-card-authoring/SKILL.md) |
| 提示词文体：世界书条目、卡文本字段、规则与输出协议、变量初值与更新规则 | [.agents/skills/tavern-prompt-style/SKILL.md](.agents/skills/tavern-prompt-style/SKILL.md) |
| 世界书蓝绿灯、关键词、分类、顺序、深度、提示词 | [.agents/skills/tavern-worldbook/SKILL.md](.agents/skills/tavern-worldbook/SKILL.md) |
| MVU 条目、初值、更新规则与输出协议 | [.agents/skills/tavern-mvu/SKILL.md](.agents/skills/tavern-mvu/SKILL.md) |
| Zod 4 结构、默认值、转换与注册 | [.agents/skills/tavern-mvu-zod/SKILL.md](.agents/skills/tavern-mvu-zod/SKILL.md) |
| 正则显示、提示词清理、流式块与深度 | [.agents/skills/tavern-regex/SKILL.md](.agents/skills/tavern-regex/SKILL.md) |
| 酒馆助手脚本、EJS | [.agents/skills/tavern-scripting/SKILL.md](.agents/skills/tavern-scripting/SKILL.md) |
| HTML/Vue 前端 | [.agents/skills/tavern-frontend/SKILL.md](.agents/skills/tavern-frontend/SKILL.md) |

规则分三层：**运行时要求**以锁定实现为准；**本仓库规范**是新卡的一致写法；**参考卡设计**只在适合新卡目标时采用。已有卡迁移保留自身协议，不因套用模板而默默改名或更换数据模型。

## 用户偏好与工具强约束

写卡前分清这三类要求，不要把偏好当成硬规则，也不要因为它是偏好就忽略。

- **工具强约束**：违反会让 `check`／`build` 失败或产物出错。例如依赖必须是无凭据 HTTPS 且地址里含 `version` 所写的固定版本、`.card/` 与 `original/` 不能手改、条目 YAML 的键必须在允许列表内、引用路径不能越出 `src/`、`project.name` 必须与目录名一致。
- **本仓库规范**：新卡的一致写法，例如先定体验目标再选模块、新卡推荐用世界书承载设定、模块增删要同步条目与 `dependencies`。它是默认值；用户有明确目标时可以偏离，偏离后在报告里说明。
- **用户偏好与参考卡设计**：题材、文风、数值体系、条目划分、界面布局、开场白数量。按用户目标执行，从不照搬示例卡或参考卡；也不要把自己的偏好写成仓库规范。

三类冲突时，以运行时实现和用户明确要求为准。拿不准就按默认值做，并在报告里写清这是默认值。

## 编辑边界

- 完整导入原卡放 `imports/`，拆分编辑区放 `cards/<名称>/src/`，打包成品放 `dist/<名称>/`。界面导入与 CLI `split` 共用归档逻辑；同名同内容复用，同名不同内容加序号，不覆盖。AI 与创作者编辑同一份 `src/`，修改后直接 build，不从原卡重新拆分覆盖编辑。
- `cards/` 默认不提交 Git，只有仓库自带的官方示例按 `.gitignore` 的名单被跟踪，名单与 `maintenance/public-release.json` 的 `examples` 一致；用户自建卡留在本地。`dist/`、`imports/`、`work/` 与参考资料缓存同样不进版本库。要把自己的卡纳入版本管理，见 [docs/UPDATING.md](docs/UPDATING.md)。
- `project.original.importPath` 只记录工作区内原卡归档位置；它不是构建输入，删除 `imports/` 归档不影响工程内 `original/` 的独立校验副本。
- 写卡时人只改两类文件：`cards/<名称>/src/` 下的正文（Markdown、JavaScript、样式、界面源码）与可读配置（`src/index.yaml`、条目 YAML）。卡 JSON、PNG、指针与打包产物都由脚本生成，不手改。
- `cards/<名称>/src/index.yaml` 是卡片总入口：`角色`、`文本`、`备选开场白`、`群聊开场白`、`世界书`、`正则`、`脚本` 都在这里引用；文本字段的值是 `src/...` 文件路径。
- 世界书、正则、脚本条目各自是一个 YAML。`关联` 由工具生成，用来在重排后对上原卡里的条目；保留即可、不手改，新条目可以省略。文件没改名时，漏写 `关联` 会按原路径找回原条目，但保留 `关联` 仍是首选。条目正文用 `文件: src/...` 引用，正则用 `替换文件`。
- 世界书条目还可以写可选键 `分类: 地理/北境`：它只决定 `organize` 把设置与可移动正文放进哪个目录，不写进卡，也不改变触发、位置与注入。
- `cards/<名称>/project.json` 只记录模块、依赖、头像、`runtimeVerified`、`original` 与工具维护的 `source`／`state`；增删条目、改顺序、改引用都由索引与条目 YAML 决定。
- `cards/<名称>/内容导航.md` 是工具生成的内容导航，用来看内容落在哪个文件，不是编辑入口。
- `.card/` 由工具维护：`state.json` 保存机器兼容数据并带 sha256 校验，未知与 legacy 数据原样保留，迁移快照在 `.card/migration-v1/`；它要和工程一起备份／提交，删掉就丢失 `关联` 映射与已取消引用记录，平时不读取、不修改。
- `modules` 只是新建模板的选择记录，不是运行时开关；增删已有卡的功能要同步维护实际条目、条目 YAML 与依赖。
- `cards/<名称>/original/` 保存导入原件与基线，只读；加载时会逐个校验 sha256。
- 构建产物在仓库根目录 `dist/<名称>/`，由 `pnpm card build` 生成，不手改。
- `references/` 是锁定的参考资料区，只读；其中代码不执行，内容不自动打进卡。公开包不带参考资料实体，用 `pnpm refs restore` 按锁文件取回本地缓存。
- 新增或重命名卡片目录优先用 `pnpm card new` 与 `pnpm card split`，两者默认生成 v2；旧版目录用 `pnpm card migrate` 原地迁移。引用增删或重排后下次打包自动生效，删除引用后源文件可以保留，但不参与打包。

世界书文件按分类目录与条目名组织，不用全局编号排序。旧路径继续支持；整理已有工程见 [docs/WORLDBOOK-FILES.md](docs/WORLDBOOK-FILES.md)，动态宏与 EJS 按需读 [docs/WORLDBOOK-TEMPLATING.md](docs/WORLDBOOK-TEMPLATING.md)。

## 命令

创作者可双击根目录的 `打开角色卡工具.cmd`（Windows）或 `打开角色卡工具.sh` 使用本地界面，也可以运行 `pnpm studio`。界面使用同一拆分／检查／构建实现，增加工程改名、副本与封面操作；服务只监听 127.0.0.1。界面源码在 `tools/studio-ui/`，服务在 `tools/studio.mjs`，工程管理逻辑在 `tools/lib/studio-operations.mjs`。操作说明见 [docs/STUDIO.md](docs/STUDIO.md)。

| 命令 | 作用 |
| --- | --- |
| `pnpm setup:workspace` | 首次准备：检查环境，缺依赖或锁文件漂移时按 `--frozen-lockfile` 安装一次；不满足条件时打印操作指导并以退出码 1 结束 |
| `pnpm doctor:workspace` | 只读检查：Node 与 pnpm 版本、依赖、锁文件与本地工具；不安装、不写文件 |
| `pnpm card new <名称> [--modules worldbook,regex,script,mvu,html,vue]` | 新建卡片骨架；不带 `--modules` 时为纯文字卡 |
| `pnpm card split <PNG或JSON文件> [--name 名称]` | 导入已有卡并拆分成可编辑文件 |
| `pnpm card check <名称\|all>` | 静态检查 |
| `pnpm card build <名称\|all>` | 生成可导入产物到 `dist/<名称>/` |
| `pnpm card diff <名称\|all>` | 对比原件基线与当前编辑区 |
| `pnpm card migrate <名称\|all>` | 把旧版卡片目录原地迁移到 v2，旧快照留在 `.card/migration-v1/` |
| `pnpm card organize <名称\|all>` | 整理世界书目录：优先按条目 YAML 的 `分类` 落目录，没写 `分类` 时保留当前目录，自动更新引用、备份并校验卡片语义不变 |
| `pnpm card arrange <名称\|all> [--order prompt\|index]` | 只重排世界书总索引、连续重编 `顺序` 并同步 `display_index`，先备份完整工程；默认按常规 prompt 阅读序，`--order index` 按现有索引顺序编号 |
| `pnpm card map <名称>` | 打印内容位置索引：创作入口、设置文件、正文文件与构建项、已取消引用的原条目，以及世界书清单（条目名、分类、目录与位置设置，不含正文）和待分类条目列表 |
| `pnpm card help` | 打印用法、默认行为与模块清单 |
| `pnpm inspect-card <JSON或PNG>` | 只读结构摘要；用 `--entries` 看索引，`--entry <id>` 等定点读取，避免全文读大卡 |
| `pnpm refs restore [--source <id>]` | 按锁文件还原参考资料：只取锁定的完整提交与文件、按 sha256 校验，不改锁文件内容；公开包不带 `references/upstream/` 实体，缓存缺失时先跑它 |
| `pnpm refs search <关键词>` ／ `pnpm refs check` | 在已还原的参考资料里检索、校验完整性 |
| `pnpm refs update` | 刷新来源并重写锁文件；仅维护者有意升级锁版本时使用，日常创作和升级工作区不跑 |
| `pnpm verify` | 离线总校验：单元测试 → 官方示例逐张检查／构建 → 发布前检查；不需要参考资料 |
| `pnpm verify:refs` | 在 `pnpm refs restore` 之后校验参考资料与锁版本一致性（等价于 `pnpm refs check`） |
| `pnpm release:check` | 发布前检查：公开清单（`maintenance/public-release.json` 里登记的文件、目录与官方示例）、文档链接与常见凭据标记 |

- PowerShell 下模块列表要整体加引号：`pnpm card new my-card --modules "worldbook,regex,script,mvu,vue"`；不加引号时逗号会被 PowerShell 当成数组语法。
- 命令输出 JSON，成功为 `ok: true`，失败为 `ok: false` 并返回退出码 1。命令的完整结构见 `tools/card.mjs`。
- `card new` 的卡片名用位置参数；`split` 用 `--name`；`new` 与 `split` 不接受对方的选项；`arrange` 只接受 `--order prompt|index`；`check`、`build`、`diff`、`migrate`、`map`、`organize` 不接受选项。

## 备份与升级

要备份的是 `cards/<名称>/` 整体（含 `src/`、`.card/` 与 `original/`）；`imports/` 中尚未导入或希望独立保留的原卡也要备份，`dist/` 可重新构建。升级工作区时先备份 `cards/`，更新代码后跑 `pnpm setup:workspace`、`pnpm doctor:workspace`，再用 `pnpm card check all` 与 `pnpm card build all` 确认现有卡还能构建。旧卡只对确实要改的那张跑 `pnpm card migrate <名称>`，不要全员迁移；参考资料按锁版本用 `pnpm refs restore` 取回，`pnpm refs update` 只用于有意升级锁版本。完整流程、自建卡的版本管理与回滚方式见 [docs/UPDATING.md](docs/UPDATING.md)。

## 事实与证据

- 字段名、触发规则与接口以本地 `references/` 缓存与本卡 `src/index.yaml`／条目 YAML 为准；来源清单见 [docs/SOURCES.md](docs/SOURCES.md)。
- 外部依赖必须写在 `project.json` 的 `dependencies` 里：无凭据 HTTPS，且地址中包含 `version` 字段所写的固定版本或提交。私有资源必须内嵌，未声明的外链会让 `check` 失败。
- `runtimeVerified` 为 `null` 表示尚未在真实宿主核实，不等于支持或不支持。
- 静态检查与构建不等于真实酒馆实测。没有宿主证据时，不声称兼容某个酒馆或扩展版本。
- 不执行卡里的脚本、EJS 和界面代码。`check` 只做解析与编译级检查：JavaScript 用 acorn 解析、TypeScript 只做语法转换、Vue 做 SFC 解析与编译、EJS 编译失败目前记为警告。
- 本工作区内容限非商业使用，详见 [LICENSE](LICENSE)。文档只引用 LICENSE，不复述条款。

## 写卡规则

- 开工先明确卡名是否就是单一互动角色；`{{char}}` 是卡名宏，不代指多角色卡中的任意人物。默认用具体姓名或身份，单角色同名卡才按需用该宏。
- 默认用简洁 YAML 缩进组织世界书正文。按主题选择简短蓝灯总览与绿灯细节；树状组织可用于种族、政治、社会风俗、组织、人物关系、地理等，分类与层级由创作目标决定，不照模板限定。分类、激活与注入位置分别设计：定义前可放世界基础与制度，定义后可放关系、处境或互动依赖的设定，均不限于某一种内容；需要靠近本轮消息的机制规则可用指定深度，`角色` 默认 `系统`，有明确需求才选择用户或助手。条目默认 `不可被递归激活: true`、`不可激活其他条目: true`；创作者需要其它布局或递归时明确设计，不能把默认偏好当宿主硬要求。
- 世界书主要承载事实、人物与机制；人称、文风、篇幅等通用叙事要求通常交给酒馆预设。关键词组合可表达树状范围，不要求递归；变量地点不会天然进入关键词扫描，进阶注入需先核对扫描路径。
- MVU 先确定主 API 随正文更新或额外 API 请求更新；标记与请求分工见 [docs/MVU-MODES.md](docs/MVU-MODES.md)。标准状态栏使用 MVU 自动添加的 `<StatusPlaceHolderImpl/>`，不默认让模型输出占位符；特殊布局按创作者需求另设生产者。

- 写或改提示词前加载 [提示词与文体](.agents/skills/tavern-prompt-style/SKILL.md)，先判断是规则、人物设定、对话示例还是玩家叙事。检查事实一致性、条件与注入依赖；规则文本默认推荐客观机制式，文体可按当前对话或已有创作说明调整。开场叙事与对话示例保留需要的场面、台词和人物语气。写完按该技能的固定文风自查过一遍。沿用已有结构，同一设定的多处表示同步核对。
- 先确定体验目标再选模块。纯文字卡不需要任何模块；`html` 与 `vue` 初始模板只能选一个，之后可以手动添加多个独立界面。
- **设定放哪里是创作选择**：可以写在世界书条目，也可以写进 `角色描述`、`性格`、`场景`、`对话示例`。新卡推荐前者，因为条目能按触发条件注入、便于增删；选传统字段时，`文本` 里只引用真正要用的键，不要为了凑格式填无意义内容。两条路径都成立，同一段设定不要同时写两遍。
- 普通新卡先对照 `cards/示例-世界书创作/`；复杂卡（世界书 + MVU + Vue + 脚本 + 正则 + 多开场白）对照 `cards/示例-雾港调查局/`，MVU 结构对照 `cards/示例-MVU-Zod-Vue/`。其余 `cards/示例-*` 由旧版迁移而来，保留原有占位与写法、继续兼容，不作为新卡默认。
- `文本` 只引用非空字段，`第一条消息` 始终引用；没写进 `文本` 的 `src/text/*.md` 文件留在原地即可，不参与打包，需要时补键引用。导入卡里已有的非空值原样保留，不因默认分布丢失。
- 运行时模型只能依赖本次请求实际注入的内容。宏、变量和世界书条目都必须在读取前由可靠路径写入，否则按未知处理并给默认行为。
- 自写资源默认随卡内嵌，也可由创作者公开托管并使用固定版本外链，见 [docs/REMOTE-ASSETS.md](docs/REMOTE-ASSETS.md)。外链须登记 `dependencies`，不能使用私有地址或浮动分支。内嵌界面 HTML 经 Base64 编码以避开宿主的 `$1` 与宏替换，不是加密。
- `正则`、`脚本` 的列表顺序就是执行／加载顺序。世界书的 `世界书.条目` 列表与各条目的 `顺序` 按预期 prompt 阅读次序一起全局递增：`位置` 先按 `角色定义之前` → `角色定义之后` → `指定深度`，指定深度内 `深度` 由深到浅，已启用条目顺次编号 1..N、步长 1，未启用条目排在列表末尾维护区；后定义的编号接在前一条之后，不预留十位或百位间隔。位置是宿主固定枚举，这套编号只表达常规布局，不保证任意预设下完全一致。
- `pnpm card arrange <名称>` 专门做上面的重排：改总索引列表、连续重编 `顺序`、同步 `display_index`，并先备份完整工程。`split`、`build`、`organize` 都不会自动改 `顺序`，避免动到已有卡的预算优先级。作者注、示例消息、outlet 等层级先按实际宿主预设手工排好索引，再 `pnpm card arrange <名称> --order index`；排序会改变跨位置预算优先级，改完要在宿主的提示词预览里核对，不是无损整理。
- `构建` 可挂在文本引用（`{ 文件, 构建 }`）、世界书、正则或脚本条目上；脚本自带数据默认内联在 `数据`，超过 2000 字符时改用同名 `.data.yaml` 的 `数据文件` 引用，两者互斥。
- 导入卡里不规则的嵌套文本会出现在所属条目的 `附属文本`，归属由工具绑定；原样保留即可，没有这类字段的卡不用管。
- `.card/state.json` 与 `original/` 里的原件由脚本读写；写卡时不要手工新增、删除或改写里面的字段。
- 新建 MVU 卡使用仓库的四条目和 Zod 模板；变量列表与输出协议作为固定资产维护，普通写卡只改结构、初值和更新规则。变更协议要核对解析器、注册适配器与正则，而不是只改提示词。
- 研究样卡先做元数据索引再读必要正文。`imports/` 中的大卡可删除；技能、模板、测试不依赖私有样卡存在，也不把其脚本作为工具运行。

## 子智能体分工

- 可并行的独立任务（资料检索、局部核对、检查与报告）可委派子智能体；简单或必须串行的步骤直接完成。
- 委派时说明目标、范围与已有证据。模型与推理强度遵循当前运行配置，不在本文件固定。
- 子智能体不并发修改同一文件或同一卡片产物；超出授权或需要改变设计时交回主智能体。
- 提示词审阅可委派子智能体，也可由主 agent 完成：加载 `tavern-prompt-style`，按文本用途与用户要求核对，只报告具体问题与改写建议。不要预设其它模型的初稿有问题，也不要把可选子智能体能力变成完成任务的前提。

## 文档约定

- 文档面向使用者和维护者，写适用范围、行为、接口与流程；不写协作过程、临时进度或作废方案。
- 易漂移的数字与法律信息（资料条数、体积、许可证等）指向锁文件、原文或命令输出，不在文档里复述或推测。许可只写本工作区内容限非商业使用，详见 `LICENSE`。
- 不确定的事实写清不确定性和核实方式，不把计划写成已完成。命令行为以 `package.json` 的 script 与 `tools/` 实现为准，文档与实现不一致时修正对应一侧。
