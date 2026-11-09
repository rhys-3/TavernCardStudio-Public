# TavernCardStudio

和 AI 一起写 SillyTavern 角色卡的本地工作区。可以从零创作，也可以导入已有 PNG / JSON 修改；支持文字、世界书、变量、脚本和前端等简单或复杂卡。

## 首次使用

1. 下载并解压 [最新版工作区](https://github.com/rhys-3/TavernCardStudio-Public/releases/latest)，在你常用的 agent 中打开这个文件夹。
2. 不清楚环境怎么装，直接说：**“帮我检查并安装这个工作区需要的环境，确认角色卡工具能打开。”** 想自己安装，看 [简短安装说明](docs/INSTALL.md)。
3. 环境就绪后，Windows 双击 **`打开角色卡工具.cmd`**。可以新建卡、复制示例，或点击“导入并拆分 PNG / JSON”。也可以让 agent 直接帮你导入。

## 导入后直接说需求

在同一个工作区继续和 agent 对话就行，例如：

> 帮我看看刚导入的这张卡，把世界书整理好，再把开场改成主角刚到城里。

> 想做一张有多个种族和势力的大世界卡，先一起聊聊设定。

不用先填创作简报，也不用复制长开工指令。Codex、Claude Code、DSH 等能在项目中读写文件、运行命令的 agent 都可以参与；如果它没有读取工作区说明，提醒一句 **“先看 AGENTS.md”** 即可。只有聊天能力、不能访问本地文件的窗口，需要另行提供文件，无法直接操作工作区。

工作区把创作流程和专业知识放在 [AGENTS.md](AGENTS.md) 与 `.agents/skills/`，由 agent 按任务读取。你只管说明想要的效果、必须保留的内容，边做边补充就可以。

AI 往往只顾完成眼前这一次的要求，可能顺手改坏之前定好的东西，所以重要的改动请自己看一遍结果。定下来的设计和写法要求，可以让 agent 记进卡根的 `创作说明.md`，它改动前会先读。

## 文件放在哪里

| 目录 | 用途 |
| --- | --- |
| `imports/` | 完整导入原卡，导入时自动归档 |
| `cards/<名称>/src/` | 你和 AI 共同修改的正文、配置与代码 |
| `dist/<名称>/` | 打包后的 JSON / PNG，可以导入酒馆 |

修改完成后，让 agent 检查并打包，或在本地工具里点击“检查”“打包”。已经拆分过的卡直接继续修改，不要重新导入覆盖。备份时保存整个 `cards/<名称>/`，包括隐藏的 `.card/` 和 `original/`；自建卡默认不随 Git 提交。

## 需要时再看

- [安装环境](docs/INSTALL.md) · [本地工具怎么用](docs/STUDIO.md)
- [创作与模块选择](docs/CREATION.md) · [大世界内容与组织](docs/WORLD-DESIGN.md)
- [世界书分类与顺序](docs/WORLDBOOK-FILES.md) · [复杂卡模块联动](docs/COMPLEX-CARDS.md)
- [文件格式与完整命令](docs/ARCHITECTURE.md) · [备份与升级](docs/UPDATING.md)
- [检查范围](docs/VERIFICATION.md) · [参考资料来源](docs/SOURCES.md)

检查与打包不等于真实酒馆实测，最终效果仍需在自己的酒馆和预设里确认。本工作区内容限非商业使用，详见 [LICENSE](LICENSE)。
