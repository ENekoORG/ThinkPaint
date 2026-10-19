# ThinkPaint Flutter 工程与模块开发计划

文档版本：0.4。更新日期：2026-10-19。

统一路线为 Flutter + Dart + 自定义绘制画布。开发按十个功能模块组织，每个模块独立细化界面、业务与验证，通过共享协议增量集成。原阶段任务不再作为开发依据。

## 架构文档

- [技术路线决定](architecture/PLATFORM_DECISION.md)：Flutter 路线、工具链目标和产品约束。
- [工程结构](architecture/PROJECT_STRUCTURE.md)：应用、协议包、模块包和计划目录。
- [共享接口](architecture/SHARED_CONTRACTS.md)：数据类型、服务入口、事务、呈现和联合流程。
- [模块依赖](architecture/MODULE_DEPENDENCIES.md)：各模块提供与消费能力及装配顺序。
- [独立界面设计](architecture/UI_DESIGN_GUIDE.md)：模块自主设计范围、资料目录和公共 UI 约定。

当前交付为文档设计；计划中的 package、协议代码和应用工程由后续授权任务建立。

## 十模块目录与分支

| 模块 | 代码目录 | 分支 | 设计资料 |
| --- | --- | --- | --- |
| M01 管理 | `packages/thinkpaint_management/` | `module/management` | `docs/design/M01/` |
| M02 绘图 | `packages/thinkpaint_drawing/` | `module/drawing` | `docs/design/M02/` |
| M03 图片与档案盒 | `packages/thinkpaint_images/` | `module/images` | `docs/design/M03/` |
| M04 画布 | `packages/thinkpaint_canvas/` | `module/canvas` | `docs/design/M04/` |
| M05 搜索 | `packages/thinkpaint_search/` | `module/search` | `docs/design/M05/` |
| M06 文字与便签 | `packages/thinkpaint_text/` | `module/text` | `docs/design/M06/` |
| M07 对象编辑与排版 | `packages/thinkpaint_editing/` | `module/editing` | `docs/design/M07/` |
| M08 线索与关系 | `packages/thinkpaint_relations/` | `module/relations` | `docs/design/M08/` |
| M09 保存与导出 | `packages/thinkpaint_storage/` | `module/storage` | `docs/design/M09/` |
| M10 界面与设备适配 | `packages/thinkpaint_platform/` | `module/platform` | `docs/design/M10/` |

每包公开入口为 `lib/thinkpaint_<模块名>.dart`，私有实现按需放在 `lib/src/`，验证放在本包 `test/`。实际名称逐包列在工程结构文档，不强制建立空的多层结构。

## 共享目录和修改责任

`apps/thinkpaint/` 是唯一应用与装配目录，维护入口、模块注册、构建配置、Android/Windows 宿主和联合验证。横向工作计划使用 `integration/assembly` 分支；本轮不创建该分支或工程。

`packages/thinkpaint_contracts/` 提供纯 Dart 数据、服务、类型注册和编辑规则。`packages/thinkpaint_flutter_contracts/` 提供 Flutter 呈现、工具和界面贡献。公共文件由管理对话协调，按语义主责维护。

M07 负责对象与事务，M04 负责坐标和呈现约定，M09 负责资源、项目和保存导出约定，M10 负责输入与公共 UI 约定。其他模块维护自己的专用能力与数据适配。

## 依赖规则

应用可以导入十模块和两个协议包。功能模块只引用共享协议、第三方依赖和自身公开入口，不能互相 import；纯 Dart 协议不依赖 Flutter 或功能包。模块通过构造参数获得所需服务，UI 工厂延迟挂载。

运行时允许跨模块协作，但不能形成构造函数中相互调用或启动的循环。资源仓与项目仓先建立，再建立对象会话和渲染；保存调度与导出协调器在所需会话和渲染能力就绪后建立。具体顺序见共享接口。

M01 管理切换流程、M03 管理图片收纳、M05 管理搜索定位。应用只装配与维护公共生命周期，不接管这些业务。

## 模块独立精细设计

各模块对话在自己的任务卡、设计目录和模块需求中细化界面与功能，保留原 58 个细项及验收。设计可以是页面、面板、工具覆盖层、设置项或状态流程，由模块选择实际承载方式。

模块内部的布局、局部导航、信息层级和交互由模块对话调研和审阅。M10 统一主题、公共导航、焦点、输入与生命周期，通过 Page/Panel/Tool/Settings 贡献承载模块界面。涉及公共协议或跨模块行为的变化由主责协调。

M01 的暖纸色、平板优先、文件夹与收藏主页及独立设置行继续作为已有设计基线。各模块独立 worktree 的成果与审阅记录继续维护，公共规划通过普通合并接入，保留已有提交与未提交设计。

## 下一轮工作的入口

1. 模块对话阅读新架构，核对自身计划目录、提供和消费接口，把已有方案映射到统一 Flutter 路线。
2. 在模块对话中完成精细化设计、必要原型与审阅，记录接口需求和联合验收；本轮不写功能代码。
3. 后续获授权的装配任务建立统一工程、两个协议包和最小注册入口，记录真实工具链与构建结果。
4. 模块在实际工程和最小接口就绪、对应授权明确后，按细项实现私有能力并提交可验证增量。
5. 真实内容接入后执行联合场景和两端验证，再按授权集成。

这组步骤是工作条件，模块不需要等待其他类别的全部功能完成。接口已定的设计与私有能力可分别推进；缺实现或设备的部分保留待集成或待验证状态。

## 联合验收

| 场景 | 业务主责 | 参与方 | 核心条件 |
| --- | --- | --- | --- |
| 打开切换与视图恢复 | M01 | M07、M09、M04、M10 | 源会话和保存版本正确，目标准备后再激活，迟到恢复不覆盖定位 |
| 图片批注 | M03 | M02、M06、M07、M04 | 绑定目标、变换、裁剪恢复和撤销一致 |
| 收纳与改接 | M03 | M08、M07、M09、M04 | 同一快照预览、版本校验、一次提交与撤销、回环保留 |
| 搜索定位 | M05 | M01、M03、M04、M07、M08 | 正式资料可检索，目标重解析，盒与临时显示正确 |
| 专注与导出 | M08/M05、M09 | M04、各内容模块、M10 | 查看条件不改数据，输出范围明确，资源与呈现完整 |
| 生命周期与恢复 | M10、M09 | 当前工具、M06、M07、M04 | 输入结束或取消、实际保存与恢复状态一致 |

## 分支与同步

十模块分支已建立，主仓库保持管理用途，各模块使用独立 worktree。领取时核对实际基线和已有改动，不能把早期共同提交当作永久固定 HEAD。

新公共文档先在管理仓库完成；按对应授权提交和发布后，模块负责人把新基线安全同步到自己的分支，保留已有设计、提交与未提交改动。同步采用普通合并或快进，保留模块历史与设计记录；正在编辑的 worktree 等停止写入后再处理，不强制更新其分支或覆盖工作区。具体批次与结果见管理仓库 PROGRESS.md。

任务卡位于 `docs/tasks/Mxx.md`，可复制消息见 [模块任务说明](MODULE_TASK_PROMPTS.md)。设计资料位于各自 `docs/design/Mxx/`，实际创建、实现、提交、推送和发布分别按当前对话授权执行。

## 完成标准

架构文档的完成条件为十模块目录、接口主责、静态依赖、运行时协作、UI 自主范围和任务说明一致。真实工程完成还需后续 Dart 分析、Flutter 构建、单元与联合测试、Windows 和 Android 设备验证。
