# ThinkPaint 模块协作约定

更新日期：2026-10-19。架构协议版本：0.1。

统一采用 Flutter + Dart + 自定义绘制画布。具体数据类型、接口签名和语义以 [共享接口](architecture/SHARED_CONTRACTS.md) 为准，计划代码目录以 [工程结构](architecture/PROJECT_STRUCTURE.md) 为准，十模块提供与消费关系见 [模块依赖](architecture/MODULE_DEPENDENCIES.md)。

## 公共层次

纯 Dart 协议包定义身份、几何、不可变对象、事务、资源和服务端口。Flutter 协议包定义呈现、工具和 UI 贡献。十模块依赖这些公共约定，由唯一应用装配实际实现；模块之间不直接导入彼此代码。

## 主责

| 能力 | 主责 | 协作方式 |
| --- | --- | --- |
| 正式对象、归属、事务和撤销 | M07 | DocumentReadPort、EditorPort、BoardSessionPort 与纯 EditRule |
| 坐标、视图、内容呈现与显示缓存 | M04 | ViewportPort、GeometryProvider、ObjectPainter、SceneRenderPort |
| 路径、箭头样式与线条文字 | M02 | LineGeometryPort；与 M08 保持同一线对象 |
| 图片、档案盒和批注业务 | M03 | ImagesPort；收纳流程由 M03 编排 |
| 文字和共享编辑交互 | M06 | TextEditingFactory；字段与位置归宿主对象 |
| 关系绑定、公共资料与关系专注 | M08 | RelationsPort、MetadataPort 及同事务纯规则 |
| 项目、资源、目录持久化、保存与导出 | M09 | 分开的资源仓、项目仓、保存协调和导出端口 |
| 资料索引、筛选和结果定位 | M05 | SearchPort、SearchPanelFactory；索引可重建 |
| 主页与画板管理流程 | M01 | BoardNavigationPort、ManagementPageFactory |
| 标准输入、公共外壳、主题和系统能力 | M10 | PlatformInputPort、ToolRuntimePort、SystemAccessPort、SettingsPort、AppShellFactory |

## 数据和操作

M07 提供唯一正式对象状态，专用内容由所属模块定义，公共资料保存一次。M08 的绑定扩展与 M02 的路径挂在同一个线对象上。视觉重叠不自动改变归属或绑定。

规划和校验使用不可变快照；完整编辑包含图片归属、关系端点、几何及资源引用。预览过期时重新规划和展示，成功一次提交、一次通知和一次撤销。关系维护不能在提交后另补第二笔编辑。

M09 固定快照版本保存，M05 消费正式变化建立索引。视图、索引、缩略图和渲染缓存均不成为另一份可独立修改的正文。内容、视图和目录版本分别报告。

## 跨模块流程

打开切换由 M01 协调实际项目、工具结束、保存和目标视图；图片收纳由 M03 协调关系改接与编辑事务；搜索定位由 M05 协调打开、展开与聚焦。应用负责装配，服务通过注入的端口协作。

系统输入由 M10 采集，M04 转换坐标，当前模块处理专用工具；输入法优先于画板快捷键。高频样本走工具会话，正式数据变化在完整操作后通知保存与索引。

## 模块独立界面设计

每模块独立细化界面和功能，资料放在 `docs/design/Mxx/`，按需要准备调研、流程、原型、视觉稿和审阅记录。M10 提供公共主题、壳、焦点与导航，模块用 Page/Panel/Tool/Settings 贡献接入，内部布局和局部状态由模块维护。

具体设计范围、资料结构与审阅步骤见 [独立界面设计约定](architecture/UI_DESIGN_GUIDE.md)。公共路由、接口或联合行为变化先与主责协调，局部设计在既有约定内独立推进。

## 变更与验证

公共协议变更记录版本、主责、受影响模块和迁移安排。模块按实际基线与独立授权工作，保护其他 worktree 的已有成果。未知类型、文件错误、未实现接口和缺设备必须明确表达，不能静默忽略后报告完成。

当前共享接口是文档设计。真实协议代码、工程和设备验收由后续授权任务建立并验证。
