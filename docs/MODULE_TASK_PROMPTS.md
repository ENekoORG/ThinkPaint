# ThinkPaint 各模块可复制任务说明

更新日期：2026-10-19。统一技术路线为 Flutter + Dart + 自定义绘制画布。

每段消息可以独立交给一个模块对话。当前工作是模块界面与功能精细设计及架构核对；本轮不写功能代码或创建工程。各模块在公共协议内独立设计页面、面板、工具和局部状态，由自己的对话组织审阅。已有方案和审阅决定继续使用；候选与采用方案分别记录。

架构入口：[技术决定](architecture/PLATFORM_DECISION.md)、[工程结构](architecture/PROJECT_STRUCTURE.md)、[共享接口](architecture/SHARED_CONTRACTS.md)、[模块依赖](architecture/MODULE_DEPENDENCIES.md)、[界面设计约定](architecture/UI_DESIGN_GUIDE.md)。

同步状态见管理仓库的 [进度](../PROGRESS.md)。先读自己独立 worktree 的任务卡、设计资料和当前对话授权，再读公共架构；尚未同步时，可只读参考管理仓库的公共文档。不能用主仓库简版任务卡替换模块完整记录，也不在仍有写入的目录中执行合并。

## 模块入口

| 模块 | 分支 | 计划代码目录 | 任务卡 |
| --- | --- | --- | --- |
| M01 管理 | `module/management` | `packages/thinkpaint_management/` | [M01](tasks/M01.md) |
| M02 绘图 | `module/drawing` | `packages/thinkpaint_drawing/` | [M02](tasks/M02.md) |
| M03 图片与档案盒 | `module/images` | `packages/thinkpaint_images/` | [M03](tasks/M03.md) |
| M04 画布 | `module/canvas` | `packages/thinkpaint_canvas/` | [M04](tasks/M04.md) |
| M05 搜索 | `module/search` | `packages/thinkpaint_search/` | [M05](tasks/M05.md) |
| M06 文字与便签 | `module/text` | `packages/thinkpaint_text/` | [M06](tasks/M06.md) |
| M07 对象编辑与排版 | `module/editing` | `packages/thinkpaint_editing/` | [M07](tasks/M07.md) |
| M08 线索与关系 | `module/relations` | `packages/thinkpaint_relations/` | [M08](tasks/M08.md) |
| M09 保存与导出 | `module/storage` | `packages/thinkpaint_storage/` | [M09](tasks/M09.md) |
| M10 界面与设备适配 | `module/platform` | `packages/thinkpaint_platform/` | [M10](tasks/M10.md) |

## M01 管理

```text
你负责 ThinkPaint M01「管理」。统一路线已定为 Flutter + Dart + 自定义绘制画布。先读本模块独立 worktree 内的 docs/tasks/M01.md、已有设计和审阅记录，再读 docs/architecture/ 五份架构文档；本地尚未同步时只读参考管理目录 D:\CodexWorkFolder\ThinkPaint 的公共架构。

使用 module/management 的独立 worktree，先核对已有目录、分支、基线和改动；已有工作保留并复用。计划代码目录是 packages/thinkpaint_management/，共享协议通过公开入口引用，模块之间不直接 import。

本轮进行本模块界面与功能精细设计，不写功能代码、不建工程。保留已有主页与业务方案，核对新工程目录、切换保存回执、设置导航和视图恢复接口。可更新自己的任务卡、docs/design/M01/ 设计资料和本模块需求细化，保留原需求与验收。已有采用方案继续细化；存在实质分歧时给出 2～3 套选择和推荐，审阅后记录采用方案。交付流程、状态、必要原型或视觉稿、接口需求与验收计划。

对照共享接口列出提供/消费能力、联合流程和验收，公共主题/导航/接口变化先协调。工程和接口实际建立后，按独立授权进入实现。本消息不授予提交、推送、合并或发布权限。
```

## M02 绘图

```text
你负责 ThinkPaint M02「绘图」。统一路线已定为 Flutter + Dart + 自定义绘制画布。先读本模块独立 worktree 内的 docs/tasks/M02.md、已有设计和审阅记录，再读 docs/architecture/ 五份架构文档；本地尚未同步时只读参考管理目录 D:\CodexWorkFolder\ThinkPaint 的公共架构。

使用 module/drawing 的独立 worktree，先核对已有目录、分支、基线和改动；已有工作保留并复用。计划代码目录是 packages/thinkpaint_drawing/，共享协议通过公开入口引用，模块之间不直接 import。

本轮进行本模块界面与功能精细设计，不写功能代码、不建工程。细化画笔、压力、鼠标、平滑、擦除和箭头的工具界面，核对活动预览、完成提交、取消与绑定接口。可更新自己的任务卡、docs/design/M02/ 设计资料和本模块需求细化，保留原需求与验收。已有采用方案继续细化；存在实质分歧时给出 2～3 套选择和推荐，审阅后记录采用方案。交付流程、状态、必要原型或视觉稿、接口需求与验收计划。

对照共享接口列出提供/消费能力、联合流程和验收，公共主题/导航/接口变化先协调。工程和接口实际建立后，按独立授权进入实现。本消息不授予提交、推送、合并或发布权限。
```

## M03 图片与档案盒

```text
你负责 ThinkPaint M03「图片与档案盒」。统一路线已定为 Flutter + Dart + 自定义绘制画布。先读本模块独立 worktree 内的 docs/tasks/M03.md、已有设计和审阅记录，再读 docs/architecture/ 五份架构文档；本地尚未同步时只读参考管理目录 D:\CodexWorkFolder\ThinkPaint 的公共架构。

使用 module/images 的独立 worktree，先核对已有目录、分支、基线和改动；已有工作保留并复用。计划代码目录是 packages/thinkpaint_images/，共享协议通过公开入口引用，模块之间不直接 import。

本轮进行本模块界面与功能精细设计，不写功能代码、不建工程。独立细化图片编辑、档案盒、收纳预览和批注交互，保持图片归属与关系改接的一次事务。可更新自己的任务卡、docs/design/M03/ 设计资料和本模块需求细化，保留原需求与验收。已有采用方案继续细化；存在实质分歧时给出 2～3 套选择和推荐，审阅后记录采用方案。交付流程、状态、必要原型或视觉稿、接口需求与验收计划。

对照共享接口列出提供/消费能力、联合流程和验收，公共主题/导航/接口变化先协调。工程和接口实际建立后，按独立授权进入实现。本消息不授予提交、推送、合并或发布权限。
```

## M04 画布

```text
你负责 ThinkPaint M04「画布」。统一路线已定为 Flutter + Dart + 自定义绘制画布。先读本模块独立 worktree 内的 docs/tasks/M04.md、已有设计和审阅记录，再读 docs/architecture/ 五份架构文档；本地尚未同步时只读参考管理目录 D:\CodexWorkFolder\ThinkPaint 的公共架构。

使用 module/canvas 的独立 worktree，先核对已有目录、分支、基线和改动；已有工作保留并复用。计划代码目录是 packages/thinkpaint_canvas/，共享协议通过公开入口引用，模块之间不直接 import。

本轮进行本模块界面与功能精细设计，不写功能代码、不建工程。细化画布导航和查看反馈，核对坐标、几何命中、按视野准备资源、同步绘制和导出接口。可更新自己的任务卡、docs/design/M04/ 设计资料和本模块需求细化，保留原需求与验收。已有采用方案继续细化；存在实质分歧时给出 2～3 套选择和推荐，审阅后记录采用方案。交付流程、状态、必要原型或视觉稿、接口需求与验收计划。

对照共享接口列出提供/消费能力、联合流程和验收，公共主题/导航/接口变化先协调。工程和接口实际建立后，按独立授权进入实现。本消息不授予提交、推送、合并或发布权限。
```

## M05 搜索

```text
你负责 ThinkPaint M05「搜索」。统一路线已定为 Flutter + Dart + 自定义绘制画布。先读本模块独立 worktree 内的 docs/tasks/M05.md、已有设计和审阅记录，再读 docs/architecture/ 五份架构文档；本地尚未同步时只读参考管理目录 D:\CodexWorkFolder\ThinkPaint 的公共架构。

使用 module/search 的独立 worktree，先核对已有目录、分支、基线和改动；已有工作保留并复用。计划代码目录是 packages/thinkpaint_search/，共享协议通过公开入口引用，模块之间不直接 import。

本轮进行本模块界面与功能精细设计，不写功能代码、不建工程。独立细化查询、筛选、结果与定位界面，核对正式变化、索引版本、盒内展开和临时显示流程。可更新自己的任务卡、docs/design/M05/ 设计资料和本模块需求细化，保留原需求与验收。已有采用方案继续细化；存在实质分歧时给出 2～3 套选择和推荐，审阅后记录采用方案。交付流程、状态、必要原型或视觉稿、接口需求与验收计划。

对照共享接口列出提供/消费能力、联合流程和验收，公共主题/导航/接口变化先协调。工程和接口实际建立后，按独立授权进入实现。本消息不授予提交、推送、合并或发布权限。
```

## M06 文字与便签

```text
你负责 ThinkPaint M06「文字与便签」。统一路线已定为 Flutter + Dart + 自定义绘制画布。先读本模块独立 worktree 内的 docs/tasks/M06.md、已有设计和审阅记录，再读 docs/architecture/ 五份架构文档；本地尚未同步时只读参考管理目录 D:\CodexWorkFolder\ThinkPaint 的公共架构。

使用 module/text 的独立 worktree，先核对已有目录、分支、基线和改动；已有工作保留并复用。计划代码目录是 packages/thinkpaint_text/，共享协议通过公开入口引用，模块之间不直接 import。

本轮进行本模块界面与功能精细设计，不写功能代码、不建工程。细化文字框、便签、输入法与共享编辑界面，核对文字提交取消、键盘避让和宿主字段归属。可更新自己的任务卡、docs/design/M06/ 设计资料和本模块需求细化，保留原需求与验收。已有采用方案继续细化；存在实质分歧时给出 2～3 套选择和推荐，审阅后记录采用方案。交付流程、状态、必要原型或视觉稿、接口需求与验收计划。

对照共享接口列出提供/消费能力、联合流程和验收，公共主题/导航/接口变化先协调。工程和接口实际建立后，按独立授权进入实现。本消息不授予提交、推送、合并或发布权限。
```

## M07 对象编辑与排版

```text
你负责 ThinkPaint M07「对象编辑与排版」。统一路线已定为 Flutter + Dart + 自定义绘制画布。先读本模块独立 worktree 内的 docs/tasks/M07.md、已有设计和审阅记录，再读 docs/architecture/ 五份架构文档；本地尚未同步时只读参考管理目录 D:\CodexWorkFolder\ThinkPaint 的公共架构。

使用 module/editing 的独立 worktree，先核对已有目录、分支、基线和改动；已有工作保留并复用。计划代码目录是 packages/thinkpaint_editing/，共享协议通过公开入口引用，模块之间不直接 import。

本轮进行本模块界面与功能精细设计，不写功能代码、不建工程。独立细化选择、对象列表和排版交互，核对对象归属、事务版本、规则扩展、撤销和会话激活。可更新自己的任务卡、docs/design/M07/ 设计资料和本模块需求细化，保留原需求与验收。已有采用方案继续细化；存在实质分歧时给出 2～3 套选择和推荐，审阅后记录采用方案。交付流程、状态、必要原型或视觉稿、接口需求与验收计划。

对照共享接口列出提供/消费能力、联合流程和验收，公共主题/导航/接口变化先协调。工程和接口实际建立后，按独立授权进入实现。本消息不授予提交、推送、合并或发布权限。
```

## M08 线索与关系

```text
你负责 ThinkPaint M08「线索与关系」。统一路线已定为 Flutter + Dart + 自定义绘制画布。先读本模块独立 worktree 内的 docs/tasks/M08.md、已有设计和审阅记录，再读 docs/architecture/ 五份架构文档；本地尚未同步时只读参考管理目录 D:\CodexWorkFolder\ThinkPaint 的公共架构。

使用 module/relations 的独立 worktree，先核对已有目录、分支、基线和改动；已有工作保留并复用。计划代码目录是 packages/thinkpaint_relations/，共享协议通过公开入口引用，模块之间不直接 import。

本轮进行本模块界面与功能精细设计，不写功能代码、不建工程。细化连接编辑、资料标签与关系专注，核对唯一线对象、纯关系规则和收纳改接计划。可更新自己的任务卡、docs/design/M08/ 设计资料和本模块需求细化，保留原需求与验收。已有采用方案继续细化；存在实质分歧时给出 2～3 套选择和推荐，审阅后记录采用方案。交付流程、状态、必要原型或视觉稿、接口需求与验收计划。

对照共享接口列出提供/消费能力、联合流程和验收，公共主题/导航/接口变化先协调。工程和接口实际建立后，按独立授权进入实现。本消息不授予提交、推送、合并或发布权限。
```

## M09 保存与导出

```text
你负责 ThinkPaint M09「保存与导出」。统一路线已定为 Flutter + Dart + 自定义绘制画布。先读本模块独立 worktree 内的 docs/tasks/M09.md、已有设计和审阅记录，再读 docs/architecture/ 五份架构文档；本地尚未同步时只读参考管理目录 D:\CodexWorkFolder\ThinkPaint 的公共架构。

使用 module/storage 的独立 worktree，先核对已有目录、分支、基线和改动；已有工作保留并复用。计划代码目录是 packages/thinkpaint_storage/，共享协议通过公开入口引用，模块之间不直接 import。

本轮进行本模块界面与功能精细设计，不写功能代码、不建工程。细化保存恢复、导入结果和导出预览，核对快照版本、资源引用、目录投影与呈现接口，物理格式形成单独方案。可更新自己的任务卡、docs/design/M09/ 设计资料和本模块需求细化，保留原需求与验收。已有采用方案继续细化；存在实质分歧时给出 2～3 套选择和推荐，审阅后记录采用方案。交付流程、状态、必要原型或视觉稿、接口需求与验收计划。

对照共享接口列出提供/消费能力、联合流程和验收，公共主题/导航/接口变化先协调。工程和接口实际建立后，按独立授权进入实现。本消息不授予提交、推送、合并或发布权限。
```

## M10 界面与设备适配

```text
你负责 ThinkPaint M10「界面与设备适配」。统一路线已定为 Flutter + Dart + 自定义绘制画布。先读本模块独立 worktree 内的 docs/tasks/M10.md、已有设计和审阅记录，再读 docs/architecture/ 五份架构文档；本地尚未同步时只读参考管理目录 D:\CodexWorkFolder\ThinkPaint 的公共架构。

使用 module/platform 的独立 worktree，先核对已有目录、分支、基线和改动；已有工作保留并复用。计划代码目录是 packages/thinkpaint_platform/，共享协议通过公开入口引用，模块之间不直接 import。

本轮进行本模块界面与功能精细设计，不写功能代码、不建工程。细化外壳、主题、设置与输入反馈，接收模块独立界面贡献；统一焦点和设备能力，保留各模块内部设计权。可更新自己的任务卡、docs/design/M10/ 设计资料和本模块需求细化，保留原需求与验收。已有采用方案继续细化；存在实质分歧时给出 2～3 套选择和推荐，审阅后记录采用方案。交付流程、状态、必要原型或视觉稿、接口需求与验收计划。

对照共享接口列出提供/消费能力、联合流程和验收，公共主题/导航/接口变化先协调。工程和接口实际建立后，按独立授权进入实现。本消息不授予提交、推送、合并或发布权限。
```
