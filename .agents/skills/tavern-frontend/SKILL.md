---
name: tavern-frontend
description: 在 TavernCardStudio 编写卡内 HTML/Vue 楼层界面，连接占位符正则、MVU 状态和单文件构建，处理 iframe 布局与生命周期。
---

# 卡内楼层界面

先读当前卡 `src/index.yaml`、相关条目 YAML 与 [前端工作流](../../../docs/FRONTEND.md)。遇到构建、依赖或宿主接口问题再读 [运行时参考](references/iframe-runtime.md)；只有接入 MVU 时读 [变量契约](../../../docs/VARIABLES.md)。

## 显示链路

1. MVU 卡由框架自动向楼层补 `<StatusPlaceHolderImpl/>`，不要求模型输出；没有 MVU 的独立界面才由开场白和输出条目提供 `<CardStatus/>`。
2. 仅显示正则将占位符替换为 HTML 围栏代码块；酒馆助手按渲染条件创建 iframe。
3. iframe 中的 loader 解码随卡保存的完整 Base64 页面，运行单文件 HTML。
4. 另一条仅提示词正则清除模型历史里的占位符，原消息不被改写。

默认完整页面保存在渲染占位符的正则条目 `替换文件` 里，楼层原文只存占位符。MVU 固定标记不要随意改名；特殊布局或多个界面才另设计标记生产者，并同步相关开场、输出约定与正则 `查找`，避免同时显示两个面板。增添内嵌界面在新正则条目的 `构建` 上登记 `入口`。不要把构建后的 HTML 手塞进聊天原文。

## 按本仓库环境实现

- HTML/Vue 用 Vite + singlefile；脚本用 esbuild。没有上游 webpack 的自动导入、`@util/*` 别名或自动 CDN 转换，Vue 等包必须显式 import 并能在本地解析。
- 自写界面默认随卡内嵌；创作者可选择 [公开固定版本外链](../../../docs/REMOTE-ASSETS.md)，先构建单 HTML、发布到自己的仓库，再配置加载页和依赖。不要把 GitHub blob 页面、Vue/TS 源码当运行地址。Vite 配置由工具提供，不自动执行卡目录中的配置。
- `$`、`_`、`z` 等是宿主运行时全局，不是本仓库自动导入。使用注册适配器时沿用宿主同一个 z，见 [Zod 规则](../../../docs/MVU-ZOD.md)。
- 现有示例与 `templates/modules/{html,vue}/ui/` 可作起点。上游 `defineMvuDataStore` 依赖 Pinia/VueUse 等，未移植前不直接引用。

## MVU 界面

- 先等待 `waitGlobalInitialized('Mvu')`；提供加载失败/超时状态。界面卸载后到达的异步结果不得重新创建定时器。
- 在楼层 iframe 用 `getCurrentMessageId()` 固定读取该楼层，不能用 latest 让所有历史面板显示最新状态。
- 读取 `Mvu.getMvuData(option)?.stat_data`，存在且结构合理后再展示；不为渲染默认值而写回游戏状态。
- 数据是读取快照，需要事件触发重新读取或有清理机制的轮询。事件签名查本地 types/MVU 源码。
- 新 Zod 卡字段就是 schema 声明的值；`[值, 说明]` 仅是部分旧卡兼容形态，不自动拆开所有数组。
- 用户操作要写回时保留整张变量表，明确目标楼层；不要无条件重复应用已经由 MVU 处理的更新块。
- Vue 卸载及 pagehide 时清理 interval、监听和应用挂载；照当前模板处理生命周期。

## 适配与验证

界面文字给玩家看：用世界里的叫法或日常用语，不显示变量路径、字段名与调试信息；调试输出放在控制台或开发开关后面。

按实际聊天容器宽度设计，避免横向溢出。iframe 高度由内容决定，谨慎使用 vh、绝对定位和嵌套滚动；颜色、布局按用户目标选择，不强制某一视觉风格。

`pnpm card check <卡名>`、`pnpm card build <卡名>`，确认得到单文件内嵌 HTML。本地 UI 测试只证明本地交互；真实宿主还要看渲染条件、宏值、MVU 时序和历史楼层刷新，未核实时 `runtimeVerified` 保持 null。
