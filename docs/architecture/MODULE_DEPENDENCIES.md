# ThinkPaint 模块目录、职责与依赖

更新日期：2026-10-19。统一技术路线：Flutter + Dart + 自定义绘制画布。

本文规定十个功能模块的代码归属、共享服务依赖和应用装配顺序。目录和接口是工程建立时使用的规划；当前工作范围为文档，尚未创建这些 package、应用工程或可调用接口。

需求细项仍以 [需求索引](../REQUIREMENTS.md) 及各模块需求为准。共享接口的数据和操作约定见 [共享接口](SHARED_CONTRACTS.md)，跨模块业务语义见 [模块协作约定](../MODULE_CONTRACTS.md)。

## 静态依赖规则

静态依赖指 Dart 文件中的 import 和 package 依赖。运行时协作指模块通过注入的共享接口使用其他模块提供的能力，两者分别管理。

```text
apps/thinkpaint
  ├── packages/thinkpaint_management
  ├── packages/thinkpaint_drawing
  ├── packages/thinkpaint_images
  ├── packages/thinkpaint_canvas
  ├── packages/thinkpaint_search
  ├── packages/thinkpaint_text
  ├── packages/thinkpaint_editing
  ├── packages/thinkpaint_relations
  ├── packages/thinkpaint_storage
  └── packages/thinkpaint_platform

各功能 package ──→ packages/thinkpaint_contracts
需要 Flutter 的功能 package ──→ packages/thinkpaint_flutter_contracts
packages/thinkpaint_flutter_contracts ──→ packages/thinkpaint_contracts
```

1. `apps/thinkpaint` 是唯一集中导入全部功能模块的应用装配入口，通过构造函数注入接口实现，注册内容类型、编辑规则、页面、工具和面板。
2. 十个功能 package 之间不得相互 import，不得通过转导出其他功能 package 绕过限制；各模块仅公开自己的模块入口和必要实现工厂。
3. `thinkpaint_contracts` 使用纯 Dart，承载值类型、不可变数据、共享服务接口、内容规则和 `EditRule`，不依赖 Flutter、`dart:ui`、平台插件或功能 package。
4. `thinkpaint_flutter_contracts` 承载全部 `*Factory`、`SceneRenderPort`、Flutter 绘制及界面贡献协议，仅依赖 Flutter 和纯 Dart 协议。纯 Dart 对象资料不包含 `Widget`、`Canvas` 或 `ui.Image`。
5. 接口的业务主责由所属模块维护，接口物理定义由共享协议目录集中承载。修改先评估调用方，由主责与管理对话协调后更新。
6. 装配入口负责创建实例和连接接口。画板切换归 M01，搜索定位归 M05，档案盒收纳归 M03，业务流程不得集中迁入应用入口。
7. 使用窄接口和显式依赖，不引入全局服务查找器；事务通知用于观察完整状态，不能用通用消息广播替代需要返回结果的业务调用。

## 目录与接口主责

各模块的单元及组件验证归其 package 的 `test/`；需要实际应用、双端或多模块的联合检查归 `apps/thinkpaint/integration_test/`，按功能主责记录证据。目录内部分层按实际实现创建，不预先建立空实现。

| 模块 | 代码目录 | 提供的主要共享接口 | 主要职责边界 |
| --- | --- | --- | --- |
| M01 管理 | `packages/thinkpaint_management/` | `BoardNavigationPort`、`ManagementPageFactory` | 主页与目录业务、创建打开切换、分类收藏、项目操作入口；项目文件及资源处理归 M09 |
| M02 绘图 | `packages/thinkpaint_drawing/` | `LineGeometryPort`、笔迹和线条类型适配、绘图工具贡献 | 压感响应、路径、平滑、擦除、箭头样式；标准输入归 M10，绑定归 M08 |
| M03 图片 | `packages/thinkpaint_images/` | `ImagesPort`、图片和档案盒类型适配、图片工具贡献 | 图片专用编辑、多实例、档案盒、批注附着意图；正式归属和撤销归 M07 |
| M04 画布 | `packages/thinkpaint_canvas/` | `ViewportPort`、`SceneRenderPort`、画布及导航界面贡献 | 坐标视图、可见范围、内容绘制调度、导航、临时查看、离屏场景呈现；正文归 M07 及内容模块 |
| M05 搜索 | `packages/thinkpaint_search/` | `SearchPort`、`SearchPanelFactory` | 索引、查询筛选、结果界面及定位流程；索引可重建，不修改正文或持久隐藏属性 |
| M06 文字与便签 | `packages/thinkpaint_text/` | `TextEditingFactory`、文字类型适配、文字工具贡献 | 中文组合输入、文字布局、便签与共享文字编辑；宿主标签和备注归原对象 |
| M07 对象编辑与排版 | `packages/thinkpaint_editing/` | `DocumentReadPort`、`EditorPort`、`BoardSessionPort`、`EditRule` 执行入口 | 唯一正式对象状态、身份归属、通用编辑、区域排版、完整事务、撤销重做 |
| M08 线索与关系 | `packages/thinkpaint_relations/` | `RelationsPort`、`MetadataPort`、关系 `EditRule` 与工具贡献 | 端点绑定、随动重接、回环、邻接、公共资料编辑及专注范围计算 |
| M09 保存与导出 | `packages/thinkpaint_storage/` | `AssetStorePort`、`ProjectRepositoryPort`、`CatalogRepositoryPort`、`SaveCoordinatorPort`、`ExportPort` | 项目与原图、目录持久化、版本化读写、保存恢复、复制导入回收、导出文件 |
| M10 界面与设备适配 | `packages/thinkpaint_platform/` | `PlatformInputPort`、`ToolRuntimePort`、`SystemAccessPort`、`SettingsPort`、`AppShellFactory` | 应用外壳、工具会话、标准设备输入、系统访问、焦点快捷键、偏好主题及生命周期 |

`EditRule` 的类型属于纯 Dart 协议，M07 负责执行、校验和提交，各内容模块可以贡献自身规则。`SceneRenderPort` 与全部 `*Factory` 属于 Flutter 协议；其余上述服务接口采用纯 Dart 数据，不携带 Flutter 或原生系统对象。

## 各模块的界面与设计所有权

每个模块独立细化自己的界面、交互和功能行为，设计资料规划位于 `docs/design/M01/` 至 `docs/design/M10/`。架构发布规定所有权，各模块设计任务按实际需要建立资料、流程与设计稿。设计应覆盖主要流程、编辑状态、空内容、进行中、取消和真实失败，并标明需要其他模块提供的能力。

| 模块 | 规划的设计资料目录 | 独立负责的界面与交互 |
| --- | --- | --- |
| M01 | `docs/design/M01/` | 主页、文件夹收藏、画板卡片列表、项目操作和切换反馈 |
| M02 | `docs/design/M02/` | 画笔面板、压感与平滑设置、绘制预览、擦除和线形编辑 |
| M03 | `docs/design/M03/` | 图片导入与裁剪、档案盒浏览、收纳改接预览、图片批注入口 |
| M04 | `docs/design/M04/` | 画布查看、导航缩放、小地图、位置收藏、视图与临时显示反馈 |
| M05 | `docs/design/M05/` | 搜索入口、条件、结果列表、索引状态和定位反馈 |
| M06 | `docs/design/M06/` | 文字框便签、输入法与编辑状态、样式设置和共享文字编辑交互 |
| M07 | `docs/design/M07/` | 选择与对象列表、变换控制、分组区域、层次、排版预览和撤销反馈 |
| M08 | `docs/design/M08/` | 绑定重接、关系文字与资料、标签状态、逐层关系专注 |
| M09 | `docs/design/M09/` | 保存恢复状态、格式与资源错误、导出预览、范围和分页选择 |
| M10 | `docs/design/M10/` | 应用外壳、共享主题、导航与焦点规则、设备能力、公共设置承载 |

模块通过 Flutter 协议的界面贡献注册页面、面板、工具及设置扩展，应用统一装配。M10 提供外壳、主题、导航、焦点和输入约定，各业务模块设计并实现自己的界面。公共间距、颜色和反馈方式由 M10 协调，模块专用功能、操作步骤和内容状态仍由对应模块负责。

跨模块页面由业务发起方组织：M03 负责收纳和改接的联合预览，M08 提供关系影响资料；M09 负责导出预览，M04 提供真实场景呈现；M05 负责定位反馈，M01 提供切换结果。这样能够保留各模块独立设计权，同时确保整段操作只有一个明确的界面主责。

## 各模块消费与修改范围

### M01 管理

M01 负责 `packages/thinkpaint_management/` 和自己的任务卡。目录分类、收藏、最近打开、主页状态及管理操作流程属于本模块；目录的实际读写由 M09 承载。

- 消费 M09 的 `CatalogRepositoryPort`、`ProjectRepositoryPort`、`SaveCoordinatorPort`，取得真实目录、项目操作结果和已保存版本。
- 消费 M07 的 `DocumentReadPort`、`BoardSessionPort`，读取有效会话和正式版本，并发布经过校验的目标会话。
- 消费 M04 的 `ViewportPort`，捕获和恢复对应画板的有效视图；缩略图经版本化呈现和资源记录取得，不自行解析各内容类型。
- 消费 M10 的 `ToolRuntimePort`、`SystemAccessPort`、`SettingsPort`，处理离开当前工具、系统项目文件入口与主页偏好。
- 通过注入的 `SearchPanelFactory` 承载搜索入口，需要共享文字编辑时使用 `TextEditingFactory`，分别由 M05、M06 实现。

主页采用暖纸色、文件夹与收藏卡片布局，提供紧凑列表；Android 优先平板。回收站下方的设置入口独占一行，分类滚动时底部入口可见。入口位置和返回后的分类、排序、滚动恢复归 M01；设置页外壳、主题和平台操作归 M10。M01 不直接构建 M10 私有设置页面。

### M02 绘图

M02 负责 `packages/thinkpaint_drawing/` 和自己的任务卡，提供笔迹、自由路径、线条几何及箭头外观的数据和适配。

- 消费 M10 的 `PlatformInputPort`、`ToolRuntimePort`、`SettingsPort`，取得真实事件、工具占用和画笔偏好。
- 消费 M04 的 `ViewportPort`，将屏幕轨迹转换为画板或对象局部轨迹。
- 消费 M07 的 `DocumentReadPort`、`EditorPort`，提交完整笔画、样式修改或擦除结果；不私有保存第二份正式对象。
- 需要显示绑定状态时消费 M08 的 `RelationsPort`，编辑线标签时使用 M06 的 `TextEditingFactory`。
- 通过类型适配提供边界、命中、呈现、编解码和搜索资料；原图式资源需求通过 `AssetStorePort` 访问。

`LineGeometryPort` 负责共享线对象的路径、端点与线形运算。M08 可请求它计算几何结果，绑定状态仍归 M08；绑定和解除绑定保留同一线对象标识及标签。

### M03 图片

M03 负责 `packages/thinkpaint_images/` 和自己的任务卡。图片裁剪、翻转、透明度、独立实例、档案盒及附着操作由本模块描述。

- 消费 M10 的 `SystemAccessPort`、`ToolRuntimePort`，取得文件、分享、剪贴板等真实输入和工具会话。
- 消费 M09 的 `AssetStorePort`，把原图纳入项目资源，不把设备绝对路径作为项目依赖。
- 消费 M07 的 `DocumentReadPort`、`EditorPort`，统一修改对象、批注归属、盒成员和变换。
- 消费 M08 的 `RelationsPort`，取得收纳改接、回环和容器移除的影响计划；消费 `MetadataPort` 编辑公共资料。
- 消费 M04 的 `ViewportPort` 进行坐标处理，使用 M06 的 `TextEditingFactory` 编辑宿主名称和备注。

`ImagesPort` 提供图片与档案盒的查询、展开和专用操作入口。M05 通过它展开搜索目标所在档案盒。实际收纳由 M03 预览确认后交 M07 一次提交，不单独维护容器父子表。

### M04 画布

M04 负责 `packages/thinkpaint_canvas/` 和自己的任务卡，维护画板、屏幕与局部坐标转换、视图、导航和呈现调度。

- 消费 M07 的 `DocumentReadPort` 和注册的内容几何、命中、Flutter 呈现适配，取得完整场景和对象层次归属。
- 消费 M09 的 `AssetStorePort`，按视野与比例调度显示资源；原图持久化和恢复引用仍由 M09 负责。
- 消费 M10 的 `PlatformInputPort`、`ToolRuntimePort`、`SettingsPort`，处理导航手势和查看偏好。
- 通过协议中的查看条件接收 M05 筛选和 M08 专注结果，实际持久隐藏状态仍从 M07 读取。

`ViewportPort` 采用纯 Dart 坐标与视图资料。`SceneRenderPort` 位于 Flutter 协议，负责普通、区域及离屏场景呈现，供缩略图和导出复用。画布不保存各内容模块的编辑副本，也不为导出重新解释内容格式。

### M05 搜索

M05 负责 `packages/thinkpaint_search/` 和自己的任务卡，维护可重建索引及查询，不持有唯一的项目资料。

- 消费 M01 的 `BoardNavigationPort`，打开目标画板并等待其可定位会话；目录资料来自 M01 的正式目录投影。
- 消费 M07 的 `DocumentReadPort` 和完整事务通知，读取对象身份、归属、隐藏状态及注册的检索资料。
- 消费 M09 的 `ProjectRepositoryPort` 读取已保存项目资料，按正式目录范围进行跨画板索引与重建。
- 消费 M03 的 `ImagesPort`、M04 的 `ViewportPort`，依次展开容器、定位高亮；消费 M08 的 `RelationsPort`、`MetadataPort` 取得邻接和资料条件。

`SearchPort` 提供查询、索引状态和定位请求；`SearchPanelFactory` 提供 Flutter 搜索界面。定位流程归 M05：重新核对标识、请求 M01 切换、等待视图恢复、展开容器、处理明确的临时显示、定位高亮。失败分别报告，不越过保存阻止直接换板。

### M06 文字与便签

M06 负责 `packages/thinkpaint_text/` 和自己的任务卡，提供文字对象和可共享的真实输入法编辑交互。

- 消费 M10 的 `ToolRuntimePort`、`SystemAccessPort`、`SettingsPort`，处理焦点、键盘视窗、剪贴板、网址和字体偏好。
- 消费 M04 的 `ViewportPort` 和 M07 的 `DocumentReadPort`、`EditorPort`，定位编辑区域并提交正式文字变化。
- 消费 M09 的 `AssetStorePort` 处理按项目需要保存的资源；通过内容适配提供布局、呈现、编解码和搜索资料。

`TextEditingFactory` 位于 Flutter 协议，接受宿主字段和提交、取消回调，编辑过程中的组合文字只属于临时输入状态。箭头标签、图片备注和管理名称不因使用共享编辑器而生成额外独立文字框，其存储归属仍由宿主决定。

### M07 对象编辑与排版

M07 负责 `packages/thinkpaint_editing/` 和自己的任务卡，维护唯一正式对象状态、编辑会话、归属、事务及撤销重做。

- 消费注册的纯 Dart 内容类型适配与 `EditRule`，检查专用数据、资源引用、复制引用重写和联合变更。
- 选择与交互消费 M04 的 `ViewportPort`、M10 的 `ToolRuntimePort`；区域标题编辑使用 M06 的 `TextEditingFactory`。
- 需要专用预览时调用注入的几何或操作能力，最终变更仍只由 `EditorPort` 提交。

`DocumentReadPort` 返回不可变的正式快照和版本；`EditorPort` 负责预览校验、提交、撤销重做及完整事务通知；`BoardSessionPort` 负责预备和激活编辑会话。界面临时选区、工具预览和当前会话撤销不得串入其他画板。

`EditRule` 是纯规则：输入候选快照、明确操作意图与变更计划，输出补充变更或校验失败。M08 的关系随动、删除及克隆处理在同一事务提交前执行，不能先提交对象变换，再靠监听事件补改端点。规则不得自己写入状态、发起保存或触发第二次事务。

### M08 线索与关系

M08 负责 `packages/thinkpaint_relations/` 和自己的任务卡，维护关系扩展、邻接查询、元数据编辑及专注计算。

- 消费 M02 的 `LineGeometryPort`，计算绑定线、端点和回环的线形；不建立另一种不可互换的线对象。
- 消费 M07 的 `DocumentReadPort`、`EditorPort`，读取正式关系并提交绑定、重接或资料修改。
- 消费 M04 的 `ViewportPort`、M10 的 `ToolRuntimePort`，进行交互与目标定位；使用 M06 的 `TextEditingFactory` 编辑宿主资料。
- 向 M07 注册纯 `EditRule`，在对象移动、删除、复制及归属变化的候选事务中补全关系变更。

`RelationsPort` 提供邻接、绑定和改接计划；`MetadataPort` 提供公共资料读取与编辑。关系扩展和元数据随统一对象快照保存，不另建可独立修改的关系文件或元数据副本。

### M09 保存与导出

M09 负责 `packages/thinkpaint_storage/` 和自己的任务卡，组织项目级格式、原图、目录持久化、写入一致性、恢复和导出文件。

- `AssetStorePort`、`ProjectRepositoryPort`、`CatalogRepositoryPort` 消费 M10 的 `SystemAccessPort`，通过实际访问凭据读写资源、项目和目录资料。
- `SaveCoordinatorPort` 消费 M07 的 `DocumentReadPort`、`BoardSessionPort`、M04 的 `ViewportPort` 及各内容编解码适配，保存对应完整版本；消费 M10 生命周期信号安排保存。
- `ExportPort` 消费 M04 的 `SceneRenderPort` 和显式导出条件，生成 PNG/PDF 并校验输出；文件访问通过 M10 的 `SystemAccessPort`。

资源仓、项目仓和目录仓可先创建，保存协调在文档会话及视图服务具备后创建，导出服务在场景呈现具备后创建。M09 的模块入口允许分批创建这些服务，不要求在资源仓构造时已经存在画布场景呈现器，从而避免 M04 与 M09 初始化互相等待。

目录名称、分类、收藏等业务归 M01，M09 负责实际持久化、版本及有效项目核对。项目标题与目录显示资料共用明确的版本约定，旧保存快照不能覆盖已生效的新名称。设备偏好由 `SettingsPort` 管理，不能混入可携带项目资料。

### M10 界面与设备适配

M10 负责 `packages/thinkpaint_platform/` 和自己的任务卡，提供模块共用的 Flutter 外壳与系统连接。

- 消费应用注入的页面、工具、面板和设置贡献，包括 `ManagementPageFactory`、`SearchPanelFactory`、`TextEditingFactory` 及各编辑模块 UI 贡献。
- 消费 `BoardNavigationPort` 的当前画板与导航结果、`SaveCoordinatorPort` 的真实保存状态，展示对应页面和状态。
- `ToolRuntimePort` 维护唯一工具会话和焦点路由，通知当前工具及文字编辑器完成、取消或阻止离开；内容算法仍由原模块实现。
- `SystemAccessPort` 返回纯 Dart 访问凭据和实际能力；原生对象留在平台实现内部。`SettingsPort` 区分应用、设备和可携带显示设置。

Android 和 Windows 宿主生成文件位于 `apps/thinkpaint/android/`、`apps/thinkpaint/windows/`，由横向工程装配维护。M10 提出需要的注册、权限或原生桥接修改，由管理对话安排；模块不独自改共享宿主及构建文件。需要自有平台插件时先协调真实目录和主责，再创建实现。

## 运行时联合操作

| 场景 | 业务发起与编排 | 参与能力 | 一致性要求 |
| --- | --- | --- | --- |
| 画板切换 | M01 | M10 工具结束；M07 正式会话；M09 保存与预备打开；M04 视图恢复 | 目标可用且源状态获得有效保存结果后才发布切换；失败保留源会话；迟到结果不能覆盖新请求 |
| 档案盒收纳与改接 | M03 | M08 关系改接计划；M07 编辑事务；M04 联合预览 | 明确确认后一次提交归属和端点；一次撤销完整恢复；同盒双端保留回环；拖出后不自动重接 |
| 移动、删除、复制对象 | M07 | 内容类型规则；M08 纯 `EditRule`；M02 线条几何 | 关系变动在提交前补齐，观察方只收到完整事务，不读取半个操作 |
| 搜索结果定位 | M05 | M01 打开；M07 重新解析；M03 展开；M04 临时显示与定位 | 先完成目标会话及视图恢复，再定位；失效结果不定位到其他对象；不改持久隐藏和锁定 |
| 中文文字与宿主标签编辑 | M06 编辑交互，宿主提交 | M10 焦点；M07 事务；M02/M03/M08/M01 宿主字段 | 组合文字不进入正式搜索或保存；共享编辑器不改变资料归属 |
| 自动保存与恢复 | M09 | M07 一致快照；M04 视图；M10 生命周期；内容编解码 | 记录实际覆盖版本；失败保留有效文件；当前撤销、重开恢复与导航历史分别管理 |
| 缩略图与 PNG/PDF 导出 | M09 | M04 `SceneRenderPort`；内容呈现；M10 系统访问 | 复用相同呈现语义；缩略图关联来源版本；导出明确范围、筛选、背景与资源完整性 |
| 设置与主页往返 | M10 页面承载，M01 主页上下文 | `AppShellFactory`、`ManagementPageFactory`、`SettingsPort` | M01 保留独立设置入口和主页浏览上下文，各模块维护自己的偏好字段 |

服务调用可以双向协作，静态 package 依赖仍必须无环。例如 M03 通过 `RelationsPort` 请求改接计划，M08 通过注册的规则参与 M07 校验；三个功能 package 只导入协议，彼此不导入实现。

## 装配先后与初始化限制

1. 建立纯 Dart 与 Flutter 协议、系统访问、标准输入、设置，以及不依赖正式会话的内容类型和纯规则贡献。
2. 创建 M09 的资源仓、项目仓、目录仓；创建无状态线条几何能力及内容适配。
3. 创建 M07 文档读写及会话能力，注册类型和纯 `EditRule`；创建 M04 视图、场景绘制及显示资源调度。
4. 创建 M09 保存协调，再创建依赖 `SceneRenderPort` 的导出能力；注入真实工具和内容服务。
5. 创建 M01 导航、M05 查询定位等业务服务，最后创建页面、面板工厂和 M10 应用外壳。

以上是能力装配约束，不是要求某模块全部完成后才开发下一个。各模块按已可调用的最小接口增量接入，独立验证与联合验收分别记录。

为避免初始化循环，`EditRule` 只使用调用参数，不捕获尚未创建的编辑引擎或关系服务。关系模块可以先提供纯规则与几何适配，再创建依赖文档服务的 `RelationsPort` 实现。页面和面板工厂延迟构建 Widget，不在服务构造时触发打开、搜索或页面导航。注册在应用装配阶段完成，避免运行中以全局查找方式补足缺失依赖。

## 独立任务的检查与交付

领取模块任务时记录实际分支、worktree、基线、协议版本和允许修改范围。已有独立 worktree 的方案和改动由原模块保留，公共规划更新不等于分支已合并。

后续工程建立后检查 package 依赖和 Dart import，确认功能模块只依赖允许的协议及经统一决定的基础库；纯 Dart 协议无 Flutter 或平台 import；应用入口没有迁入模块业务流程。新增共享依赖、宿主配置或跨模块协议需先报告受影响模块，再由主责安排。

每个内容模块交付自身几何、命中、呈现、数据版本、编解码、引用重写及搜索资料适配；M04、M05、M07、M09 分别消费。缺少真实模块、硬件或持久化时，相关细项保留待集成或待验证状态。
