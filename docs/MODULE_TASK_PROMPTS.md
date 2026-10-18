# ThinkPaint 各模块可复制任务说明

更新日期：2026-10-19。

每个文本框可以独立复制给一个开发对话。当前任务仅进行模块准备，统一主技术路线和功能工程仍未建立。各模块分支已经发布；独立 worktree 由对应对话按任务卡检查后创建。

任务对话只更新自己的任务卡，不写功能代码，不自行选择另一套框架。技术决策和实现授权下发后，再安排具体实现。完整协作规则见 [模块任务交接](TASK_HANDOFF.md)。

## 模块入口

| 模块 | 分支 | 任务卡 |
| --- | --- | --- |
| M01 管理 | `module/management` | [M01 任务卡](tasks/M01.md) |
| M02 绘图 | `module/drawing` | [M02 任务卡](tasks/M02.md) |
| M03 图片与档案盒 | `module/images` | [M03 任务卡](tasks/M03.md) |
| M04 画布 | `module/canvas` | [M04 任务卡](tasks/M04.md) |
| M05 搜索 | `module/search` | [M05 任务卡](tasks/M05.md) |
| M06 文字与便签 | `module/text` | [M06 任务卡](tasks/M06.md) |
| M07 对象编辑与排版 | `module/editing` | [M07 任务卡](tasks/M07.md) |
| M08 线索与关系 | `module/relations` | [M08 任务卡](tasks/M08.md) |
| M09 保存与导出 | `module/storage` | [M09 任务卡](tasks/M09.md) |
| M10 界面与设备适配 | `module/platform` | [M10 任务卡](tasks/M10.md) |

## M01 管理

```text
你负责 ThinkPaint 的 M01「管理」模块。项目管理目录是 D:\CodexWorkFolder\ThinkPaint。先阅读该目录的 docs/tasks/M01.md，按其中全部约定执行。

使用已有分支 module/management，在分配的独立目录 D:\CodexWorkFolder\ThinkPaint-worktrees\management 工作；目录尚未创建时，按任务卡先检查并创建 Git worktree，不在管理目录切分支。

本轮只做模块开发准备，不写功能代码、不创建工程或安装开发依赖。先梳理主页、画板创建打开切换、分类收藏、复制回收和导入恢复的流程；明确保存失败时怎样保留当前会话。M09 执行项目文件操作，M04 恢复视图，M05 提供检索，M10 承载页面。

只更新自己的 docs/tasks/M01.md，交付细项顺序、接口需求、联合场景、验收与设备计划、阻塞项和下一轮建议。不要修改公共文档，不提交、推送、合并或发布。
```

## M02 绘图

```text
你负责 ThinkPaint 的 M02「绘图」模块。项目管理目录是 D:\CodexWorkFolder\ThinkPaint。先阅读该目录的 docs/tasks/M02.md，按其中全部约定执行。

使用已有分支 module/drawing，在分配的独立目录 D:\CodexWorkFolder\ThinkPaint-worktrees\drawing 工作；目录尚未创建时，按任务卡先检查并创建 Git worktree，不在管理目录切分支。

本轮只做模块开发准备，不写功能代码、不创建工程或安装开发依赖。先梳理画笔颜色、笔与数位板压感、鼠标路径、平滑、两种擦除和自由箭头；说明需要哪些真实设备输入与验收。M10 采集标准事件，M04 换算坐标，M07 提供事务，M08 管理绑定，M06 编辑线条文字。

只更新自己的 docs/tasks/M02.md，交付细项顺序、接口需求、联合场景、验收与设备计划、阻塞项和下一轮建议。不要修改公共文档，不提交、推送、合并或发布。
```

## M03 图片与档案盒

```text
你负责 ThinkPaint 的 M03「图片与档案盒」模块。项目管理目录是 D:\CodexWorkFolder\ThinkPaint。先阅读该目录的 docs/tasks/M03.md，按其中全部约定执行。

使用已有分支 module/images，在分配的独立目录 D:\CodexWorkFolder\ThinkPaint-worktrees\images 工作；目录尚未创建时，按任务卡先检查并创建 Git worktree，不在管理目录切分支。

本轮只做模块开发准备，不写功能代码、不创建工程或安装开发依赖。先梳理图片入口、原图与独立实例、裁剪、批量摆放、档案盒和附着批注。收纳已有连线图片必须先预览，确认后改接盒整体，并可一次撤销；同盒双端保留回环，图片拖出后不自动重接。列清 M02、M04、M06、M07、M08、M09 的协作接口。

只更新自己的 docs/tasks/M03.md，交付细项顺序、接口需求、联合场景、验收与设备计划、阻塞项和下一轮建议。不要修改公共文档，不提交、推送、合并或发布。
```

## M04 画布

```text
你负责 ThinkPaint 的 M04「画布」模块。项目管理目录是 D:\CodexWorkFolder\ThinkPaint。先阅读该目录的 docs/tasks/M04.md，按其中全部约定执行。

使用已有分支 module/canvas，在分配的独立目录 D:\CodexWorkFolder\ThinkPaint-worktrees\canvas 工作；目录尚未创建时，按任务卡先检查并创建 Git worktree，不在管理目录切分支。

本轮只做模块开发准备，不写功能代码、不创建工程或安装开发依赖。先梳理四向坐标、平移缩放、可见绘制、类型适配、小地图位置收藏和边缘平移。明确屏幕、画板与局部坐标和各类内容的注册接口；M07 维护对象状态，内容模块维护几何与编辑，M09 维护资源。

只更新自己的 docs/tasks/M04.md，交付细项顺序、接口需求、联合场景、验收与设备计划、阻塞项和下一轮建议。不要修改公共文档，不提交、推送、合并或发布。
```

## M05 搜索

```text
你负责 ThinkPaint 的 M05「搜索」模块。项目管理目录是 D:\CodexWorkFolder\ThinkPaint。先阅读该目录的 docs/tasks/M05.md，按其中全部约定执行。

使用已有分支 module/search，在分配的独立目录 D:\CodexWorkFolder\ThinkPaint-worktrees\search 工作；目录尚未创建时，按任务卡先检查并创建 Git worktree，不在管理目录切分支。

本轮只做模块开发准备，不写功能代码、不创建工程或安装开发依赖。先梳理可重建索引、真实变更通知、查询及标签状态颜色筛选、结果界面和定位流程。跨画板打开交给 M01，展开盒交给 M03，定位交给 M04，隐藏状态查询交给 M07，关系邻接交给 M08；索引不得成为内容真源。

只更新自己的 docs/tasks/M05.md，交付细项顺序、接口需求、联合场景、验收与设备计划、阻塞项和下一轮建议。不要修改公共文档，不提交、推送、合并或发布。
```

## M06 文字与便签

```text
你负责 ThinkPaint 的 M06「文字与便签」模块。项目管理目录是 D:\CodexWorkFolder\ThinkPaint。先阅读该目录的 docs/tasks/M06.md，按其中全部约定执行。

使用已有分支 module/text，在分配的独立目录 D:\CodexWorkFolder\ThinkPaint-worktrees\text 工作；目录尚未创建时，按任务卡先检查并创建 Git worktree，不在管理目录切分支。

本轮只做模块开发准备，不写功能代码、不创建工程或安装开发依赖。先梳理中文组合输入、文字与对象编辑状态、样式便签、尺寸换行、键盘避让、网址、共享文字编辑和字体往返。M07 提供撤销事务，M09 保存导出，M10 提供焦点与键盘信息；宿主标签和备注保持原对象归属。

只更新自己的 docs/tasks/M06.md，交付细项顺序、接口需求、联合场景、验收与设备计划、阻塞项和下一轮建议。不要修改公共文档，不提交、推送、合并或发布。
```

## M07 对象编辑与排版

```text
你负责 ThinkPaint 的 M07「对象编辑与排版」模块。项目管理目录是 D:\CodexWorkFolder\ThinkPaint。先阅读该目录的 docs/tasks/M07.md，按其中全部约定执行。

使用已有分支 module/editing，在分配的独立目录 D:\CodexWorkFolder\ThinkPaint-worktrees\editing 工作；目录尚未创建时，按任务卡先检查并创建 Git worktree，不在管理目录切分支。

本轮只做模块开发准备，不写功能代码、不创建工程或安装开发依赖。先梳理稳定身份、归属与变更通知、选择变换、复制删除、层次锁定隐藏、分组区域、排版和完整事务撤销。提出统一对象与事务接口供管理对话协调，不直接改其他模块；重点说明收纳和连线改接怎样一次执行、取消、撤销。

只更新自己的 docs/tasks/M07.md，交付细项顺序、接口需求、联合场景、验收与设备计划、阻塞项和下一轮建议。不要修改公共文档，不提交、推送、合并或发布。
```

## M08 线索与关系

```text
你负责 ThinkPaint 的 M08「线索与关系」模块。项目管理目录是 D:\CodexWorkFolder\ThinkPaint。先阅读该目录的 docs/tasks/M08.md，按其中全部约定执行。

使用已有分支 module/relations，在分配的独立目录 D:\CodexWorkFolder\ThinkPaint-worktrees\relations 工作；目录尚未创建时，按任务卡先检查并创建 Git worktree，不在管理目录切分支。

本轮只做模块开发准备，不写功能代码、不创建工程或安装开发依赖。先梳理端点绑定、随动、重接与回环、删除复制时的关系、来源备注、标签状态和关系专注。与 M02 共享线对象身份及几何，与 M07 共享公共元数据和事务；为 M03 提供收纳改接计划，为 M05 提供邻接和真实资料。

只更新自己的 docs/tasks/M08.md，交付细项顺序、接口需求、联合场景、验收与设备计划、阻塞项和下一轮建议。不要修改公共文档，不提交、推送、合并或发布。
```

## M09 保存与导出

```text
你负责 ThinkPaint 的 M09「保存与导出」模块。项目管理目录是 D:\CodexWorkFolder\ThinkPaint。先阅读该目录的 docs/tasks/M09.md，按其中全部约定执行。

使用已有分支 module/storage，在分配的独立目录 D:\CodexWorkFolder\ThinkPaint-worktrees\storage 工作；目录尚未创建时，按任务卡先检查并创建 Git worktree，不在管理目录切分支。

本轮只做模块开发准备，不写功能代码、不创建工程或安装开发依赖。先梳理版本化项目容器、资源引用、完整事务快照、一致写入、自动保存恢复、跨端往返和 PNG/PDF 导出。内容模块提供自身编解码，M07 提供快照，M04 提供呈现；格式选型作为提案评审，不自行定为全项目标准。

只更新自己的 docs/tasks/M09.md，交付细项顺序、接口需求、联合场景、验收与设备计划、阻塞项和下一轮建议。不要修改公共文档，不提交、推送、合并或发布。
```

## M10 界面与设备适配

```text
你负责 ThinkPaint 的 M10「界面与设备适配」模块。项目管理目录是 D:\CodexWorkFolder\ThinkPaint。先阅读该目录的 docs/tasks/M10.md，按其中全部约定执行。

使用已有分支 module/platform，在分配的独立目录 D:\CodexWorkFolder\ThinkPaint-worktrees\platform 工作；目录尚未创建时，按任务卡先检查并创建 Git worktree，不在管理目录切分支。

本轮只做模块开发准备，不写功能代码、不创建工程或安装开发依赖。先梳理应用外壳与面板、标准笔鼠触控事件、唯一工具会话、快捷键焦点、主题偏好、设备能力和前后台生命周期。M01 维护主页，M02 处理笔迹压力，M04 处理视图手势，M06 处理组合输入，M09 处理保存；不自行创建另一套工程。

只更新自己的 docs/tasks/M10.md，交付细项顺序、接口需求、联合场景、验收与设备计划、阻塞项和下一轮建议。不要修改公共文档，不提交、推送、合并或发布。
```
