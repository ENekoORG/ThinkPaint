# ThinkPaint 共享接口设计

更新日期：2026-10-19。协议版本：0.1。统一路线见 [技术路线决定](PLATFORM_DECISION.md)，代码位置见 [工程结构](PROJECT_STRUCTURE.md)。

Port 表示模块之间可调用的接口，约定输入、输出和失败语义。这里的类型与签名为实现约定，后续工程任务据此建立真实协议代码。具体业务数据和界面细节由模块独立设计。

## 位置与维护责任

纯 Dart 协议位于计划目录 `packages/thinkpaint_contracts/lib/src/models/`、`ports/` 和 `extensions/`，不依赖 Flutter、`dart:ui` 或功能模块。

Flutter 协议位于 `packages/thinkpaint_flutter_contracts/lib/src/rendering/`、`tools/` 和 `widgets/`，定义 Canvas 呈现、预览、页面、面板、工具和共享文字编辑等适配。

共享文件由管理对话协调修改，语义主责按下表分配。模块通过公开入口引用，不能访问其他模块的 `lib/src/`。

| 主责 | 纯 Dart 协议文件 | Flutter 协议文件 |
| --- | --- | --- |
| M07 | models/identity.dart、document.dart、metadata.dart、editing.dart；ports/editor.dart、board_session.dart；extensions/edit_rule.dart | tools/selection_contribution.dart |
| M04 | models/geometry.dart、viewport.dart；ports/viewport.dart；extensions/geometry_provider.dart | rendering/object_painter.dart、scene_render_port.dart |
| M09 | models/assets.dart、project.dart、saving.dart、exporting.dart；ports/asset_store.dart、project_repository.dart、catalog_repository.dart、save_coordinator.dart、export.dart；extensions/object_codec.dart | rendering/export_render_types.dart |
| M10 | models/input.dart、operations.dart、preferences.dart；ports/platform_input.dart、tool_runtime.dart、system_access.dart、settings.dart | tools/tool_contribution.dart；widgets/app_shell_factory.dart、ui_contribution.dart |
| M02 | models/line.dart；ports/line_geometry.dart | tools/drawing_contribution.dart |
| M03 | models/images.dart；ports/images.dart | widgets/image_viewer_factory.dart |
| M06 | models/text.dart | widgets/text_editing_factory.dart |
| M08 | models/relations.dart；ports/relations.dart、metadata.dart | widgets/metadata_panel_factory.dart |
| M05 | models/search.dart；ports/search.dart；extensions/search_extractor.dart | widgets/search_panel_factory.dart |
| M01 | models/catalog.dart、navigation.dart；ports/board_navigation.dart | widgets/management_page_factory.dart |

表中相对文件名用于明确归属，后续按实际需要建立。相同公共类型只定义一次。

## 公共数据类型

| 类型 | 主要字段与语义 |
| --- | --- |
| BoardId、ObjectId、SessionId、AssetId | 稳定标识；项目身份、对象身份、打开会话和原图资源分别表示 |
| Vec2、Rect2、Transform2D | 画板与局部几何值，使用 Dart double；不携带 Flutter UI 对象 |
| DocumentSnapshot | boardId、contentRevision、画板标题、不可变对象集合、资源引用及必要项目显示设置；可用于文件读取、索引和离屏呈现 |
| SceneSnapshot | sessionId 与对应 DocumentSnapshot 的只读组合；表示当前打开会话，不复制正文，不把 sessionId 写入项目 |
| ObjectRecord | id、typeId、局部变换、parentId、层次、锁定隐藏、公共资料、专用 payload、带版本扩展 |
| ObjectMetadata | 标题、来源、说明、日期、标签、状态；公共字段由 M07 定义结构、M08 提供编辑行为 |
| ObjectPayload | 内容类型的不可变专用数据接口；由所属模块实现并注册，不使用限制跨库扩展的封闭类型体系 |
| ViewSnapshot | boardId、viewRevision、中心位置、缩放及位置收藏；选区、输入会话和临时搜索显示不进入正文 |
| EditPlan | boardId、sessionId、baseRevision、完整变更、资源引用变化和操作说明 |
| CommitOutcome | 已应用及新版本、无变化、版本冲突、校验拒绝；不把业务拒绝解释为成功 |
| DocumentChange | 原因、boardId、sessionId、前后内容版本、受影响标识；提交、撤销和重做均产生正式版本 |
| AssetRef、StagedAsset | 内容标识、媒体类型、字节数及校验信息；原图先准备完整，再纳入对象事务 |
| LoadedProject、PreparedBoard | 有效 DocumentSnapshot、资源与已保存视图；准备后尚未成为当前画板，源会话保持可用 |
| SwitchGuard | 源 SessionId、源内容版本、目标准备标识；M07 校验这些状态，源视图版本由 M01 向 M04 同步核对 |
| SavedCheckpoint | 已写入的 boardId、内容版本、视图版本、有效项目位置和写入结果 |
| CatalogSnapshot、CatalogChange | 独立 catalogRevision、画板目录投影、文件夹、收藏、回收状态和变化 |
| InputSample、DeviceCapabilities | 来源、指针、逻辑像素位置、时序、压力和可用属性；不支持的能力使用明确缺失状态 |
| OperationCancelled、StorageFailure | 主动取消与预期读写失败分别表示；失败保留代码、阶段、原因和可重试信息 |
| OperationContext | 操作标识与取消信号，用于文件选择、读写、导入等实际异步边界 |
| SearchQuery、SearchResult | 查询标识、条件、索引版本及结果；结果包括稳定目标标识和仅供定位的容器路径提示 |
| VisibilityState、RevealLease | 筛选、关系专注与临时显示；不改写持久隐藏和对象数据，临时显示可释放 |
| RenderRequest | 区域、输出像素尺寸、背景与查看范围；用于同一 DocumentSnapshot 的屏幕外呈现 |
| ExportRequest、RenderedPage、ExportedArtifact | 固定快照、区域、查看范围、输出尺寸、PNG 页面数据和实际导出结果 |

图片、文字、笔迹、分组、区域、档案盒各自定义专用 payload。公共元数据随 ObjectRecord 保存一次。线条 payload 由 M02 管理路径、样式与唯一文字字段；M08 的绑定作为同一对象的带版本扩展，绑定前后不更换 ObjectId 或复制标签。

## 编辑与会话接口

| 接口 | 主要成员 | 主责与行为 |
| --- | --- | --- |
| DocumentReadPort | `SceneSnapshot? current`；`snapshot(SessionId)`；`Stream<DocumentChange> changes` | M07 提供不可变正式状态，订阅者只读 |
| EditorPort | `commit(EditPlan) -> CommitOutcome`；`undo(SessionId)`；`redo(SessionId)` | M07 在内存中校验并一次应用完整事务；不在提交期间等待文件读写 |
| BoardSessionPort | `prepare(LoadedProject)`；`activate(PreparedBoard, SwitchGuard)`；`discard(PreparedBoard)` | M07 管理会话准备与唯一当前会话；最终激活核对源会话与版本 |
| EditRule | `expand(snapshot, proposedChanges)`；`validate(candidateSnapshot)` | 纯规则提供关联变化与最终校验；不读服务当前状态、不提交嵌套事务 |

对象只具有一个正式状态。内容模块产生操作和私有预览，通过 EditorPort 改动。M07 先展开通用归属与克隆引用变化，各注册纯规则再从同一个基础候选状态计算关系随动、删除等附加变化，合并后统一校验。规则不依赖其他规则的输出，不重复触发追加或提交另一笔编辑；同一字段出现不兼容变更时拒绝本次计划。

M08 的规则只使用传入的不可变快照与变更，以及启动时可用的无状态几何能力，不捕获尚未建立的关系服务或编辑会话。通用几何变换可以由 M07 执行，依赖笔迹或文字专用内容时调用类型能力。

所有提交核对 baseRevision。预览期间正式状态已变化时，旧计划失效，重新规划并展示；不自动按旧位置提交。成功只产生一次正式变化和一个撤销步骤，失败或取消不保留半个收纳、复制或改接。

撤销与重做同样生成新 contentRevision，并恢复资源引用、归属、几何、端点与资料。当前会话历史用于编辑撤销，重开恢复使用 M09 的有效项目和备份。

## 坐标、内容接入和呈现

| 接口 | 主要成员 | 行为 |
| --- | --- | --- |
| ViewportPort | `screenToWorld`、`worldToLocal`、`currentView`、`changes`、`prepareView`、`focusObjects`、`temporaryReveal` | M04 提供坐标与视图操作和变化流，结果关联 SessionId 和 viewRevision |
| GeometryProvider | `bounds(object, context)`、`hitTest(object, localPoint, tolerance)` | 内容模块提供类型几何；M04 负责调度，M07 使用结果选择与变换 |
| ObjectPainter | `prepare(object, renderResources)`；同步 `paint(canvas, preparedObject, context)` | Flutter 呈现适配，模块读取自己的类型；准备在 paint 之前完成 |
| SceneRenderPort | `render(DocumentSnapshot, RenderRequest) -> Future<RenderedPage>` | M04 渲染明确正文快照与区域；M09 负责文件编码组合与写入 |

坐标从屏幕逻辑像素换算到画板，再换算到对象局部空间。保存原始画板坐标，进入绘制时相对当前视图原点换算，减少远坐标精度损失。图片批注和容器共同使用 M07 的归属与变换规则。

显示缓存和文字布局缓存属于可重建的呈现数据。图片原图归 M09；M04 按视野取得并释放显示句柄，M03 提供裁剪与图片呈现规则。paint 内不得读文件、启动解码或等待 Future。异步加载状态可明确显示，导出必须准备所需资源或报告失败，不能以缺图的结果报告成功。

视图返回与编辑撤销分开。持久隐藏、筛选、关系专注、临时搜索显示分别表示；释放 RevealLease 恢复原有查看条件。定位请求带会话和视图顺序，过期恢复不能覆盖较新的搜索定位。

## 输入与工具接口

| 接口 | 主要成员 | 行为 |
| --- | --- | --- |
| PlatformInputPort | 标准指针样本、设备能力、焦点与生命周期事件 | M10 采集系统实际资料，位置使用视图逻辑像素 |
| ToolRuntimePort | `activateTool`、`finishActiveEdit`、`cancelActiveEdit`、工具状态 | M10 管理唯一活动工具会话和焦点；专用动作属于模块 |
| ToolContribution | 工具标识、UI 入口、会话创建和开始/更新/结束/取消 | Flutter 协议；绘图、选择、裁剪和连线分别注册自己的工具 |

高频指针样本直接送到唯一工具会话。M04 提供坐标转换，M02 积累笔迹并提交完成操作。样本不经过通用对象事件、JSON 或逐点保存，刷新可按帧合并，原始必要样本保留顺序。

输入法组合输入由 M06 管理，优先于画板快捷键；全局焦点与按键上下文由 M10 管理。前后台、失焦与系统取消通知拥有当前操作的工具，由其完成、取消或明确拒绝离开，再处理保存。

Windows 的压力、橡皮、按钮和笔触摸共存按实际设备验证。框架没有提供的倾斜等属性不能填入模拟值并声称设备支持；必要原生扩展由 M10 提案，平台宿主配置由装配工作维护。

## 资源、项目和目录接口

| 接口 | 主要成员 | 主责与行为 |
| --- | --- | --- |
| SystemAccessPort | `pickImages`、`pickProject`、剪贴板、分享投递、`openUrl`、保存目标选择 | M10 提供系统访问句柄；取消按 OperationCancelled 表达，由界面呈现正常取消 |
| AssetStorePort | `stage(input, op)`、`read(AssetRef)`、资源存在与引用查询 | M09 保存原图和暂存，已完整准备的资源才能纳入事务 |
| ProjectRepositoryPort | `create`、`load`、`copy`、`importProject`、`recycle`、`restore`、正式项目变化流 | M09 执行真实项目操作，读取不要求激活会话，结果和失败阶段明确 |
| CatalogRepositoryPort | `readCatalog`、`watchCatalog`、文件夹/收藏/回收投影持久化 | M01 拥有目录业务，M09 承担实际持久化 |
| SaveCoordinatorPort | `save(SceneSnapshot, ViewSnapshot, op) -> Future<SavedCheckpoint>`；保存状态通知 | M09 固定保存版本，同画板写入按顺序处理 |
| ExportPort | `export(ExportRequest, op) -> Future<ExportedArtifact>` | M09 调用呈现端口输出区域、PNG 或 PDF，返回实际文件结果 |

项目标题以项目正文头部为唯一来源，目录名称是投影。活动画板通过完整编辑更新标题；关闭画板的改名由 M09 读取有效版本后更新。目录缓存不能把旧名称写回正文，旧保存快照也不能覆盖较新的标题。

目录 catalogRevision、内容 contentRevision、视图 viewRevision 各自递增。保存回执只覆盖指定版本；内容 R 写入成功时若会话已到 R+1，界面仍显示未保存。最后视图和目录资料的写入结果分别表达，不能用正文成功代替全部成功。

原图先完成暂存和校验，再进入对象引用；取消只撤回本操作的引用，仍被对象或撤销恢复记录使用的原图不可提前清理。项目存储固定公开的逻辑数据与类型版本，具体容器实现由 M09 形成方案后统一审定，业务模块不依赖内部磁盘路径。

异步文件操作使用有界 OperationContext，重复投递引用同一次操作，真正重试有新的操作标识。保存或复制结果不明确时查询实际有效项目状态，不盲目重复创建。普通同步编辑不附加通用任务执行器。

异步接口成功返回相应结果，主动取消使用 OperationCancelled，预期文件失败使用 StorageFailure；界面按真实类型区分取消和错误。编辑版本冲突通过 CommitOutcome 表达。原始异常和诊断信息保留，不以空结果或静默重试吞掉失败。

## 内容与编解码注册

纯 Dart ContentTypeDefinition 提供 typeId、数据版本、payload 与扩展编解码、数据校验、资源引用提取、克隆引用重写和可检索资料。Flutter 的 ContentUiBinding 提供几何、绘制、编辑预览和 UI 构建能力。

M02 编解码线条路径、样式和唯一文字字段，M08 编解码绑定扩展，公共元数据由 M07 的统一对象结构保存。M09 组合注册结果，不解释各模块私有内容。不支持的类型、版本或损坏资源明确拒绝或报告，不能静默丢弃后继续保存。

## 业务协作端口

| 接口 | 主责 | 主要成员与用途 |
| --- | --- | --- |
| BoardNavigationPort | M01 | `open(boardId, reason, op)`、当前画板查询；返回成功激活与初始视图就绪的结果 |
| LineGeometryPort | M02 | 查询或规划线条几何、端点位置、样式与标签变更；不改写正式数据 |
| ImagesPort | M03 | 图片与盒的定位、`planCollect`、附着计划、`revealObject`；收纳流程由 M03 编排 |
| TextEditingFactory | M06 | 构建文字编辑交互，返回提交或取消；字段和位置属于宿主对象 |
| RelationsPort | M08 | 邻接查询、绑定、重接、回环、改接计划与关系专注集合 |
| MetadataPort | M08 | 公共资料编辑计划、标签状态定义；实际字段仍在正式对象中 |
| SearchPort | M05 | 查询、索引状态和重建；结果包含来源版本与查询标识 |
| SearchPanelFactory | M05 | 构建搜索界面；点击定位流程仍由 M05 使用共享端口编排 |
| SettingsPort | M10 | 应用与设备偏好读取、写入和变化；项目显示设置经正文保存 |
| AppShellFactory | M10 | 接收页面、面板、工具和设置贡献，承载应用导航、主题与焦点 |
| ManagementPageFactory | M01 | 构建管理主页及自身交互，使用注入的服务和设计方案 |

## 三个联合流程

### 打开与切换画板

M01 结束或明确处理当前输入，获取源内容和视图版本，调用 M09 保存并准备目标。目标的项目、资源和初始视图在准备态校验；源会话在成功切换前保留。

最终激活核对源会话、内容与视图仍对应保存回执，变化时明确重新处理保存，不用旧回执离开。M04 把目标初始视图绑定到准备会话后，M01 同步核对源视图版本，紧接着调用 M07 按 SwitchGuard 激活，中间不插入异步等待。M07 校验自身维护的会话与内容版本，M01 发布成功。失败只释放目标准备状态，源会话保持可继续处理。

BoardNavigationPort 成功返回时，目标初始视图已经就绪。M05 此后展开容器、临时显示并定位目标；过期的视图恢复不能覆盖定位。同一当前画板的定位不重新加载项目或覆盖当前视图。

### 图片收纳与连线改接

M03 在一个 SceneSnapshot 上准备归属变化，M08 在同一版本准备端点改接与回环，合成一个 EditPlan。预览明确列出变更，确认时版本变化则重新预览。

EditorPort 一次校验并提交归属、线形、端点、资料和资源引用，发出一次变化。取消、校验失败和版本冲突不应用部分结果；一次撤销恢复全部内容。图片拖出不自动把盒连接接回图片。

### 搜索与索引

M05 消费正式变化、目录变化与有效项目快照，索引记录来源版本，可从正文重建。重建从 R 开始，补齐 R 之后的正式变化再切换有效索引；事件断档明确重建，旧查询结果不覆盖新查询。

点击结果时按稳定标识重新解析目标和归属。M05 通过 M01 打开画板、M03 展开盒、M07 查询隐藏状态、M04 临时显示与定位，不能把过期容器路径当作真源。

## UI 贡献和模块设计

Flutter 协议统一 PageContribution、PanelContribution、ToolContribution、SettingsContribution，标识使用 `M01.*` 等模块命名空间。每项提供构建工厂、所需能力、入口和焦点规则，工厂在挂载时延迟创建 Widget。

模块拥有自己的页面、面板、对话框、内部导航和状态，设计资料放在 `docs/design/Mxx/`。M10 规定主题颜色、字号、可访问性、顶层导航、焦点和生命周期；模块在这些公共约定内独立细化视觉与交互。业务状态和实际资料只保留对应主责真源。

设置入口由页面所属模块维护，设置页与分类容器由 M10 承载，模块注册自己的设置项。M01 的暖纸色和平板优先方案、回收站下方独立设置入口作为首批公共界面约束。

## 装配与生命周期

1. 收集无状态类型、codec、纯规则及 UI 工厂描述，先建立 M02 的无状态线形能力，检查标识唯一。
2. 建立 M10 系统访问、标准输入、工具运行时及设置服务，再建立 M09 原图资源仓与项目仓；此时不启动订阅或打开项目。
3. 建立 M07 正式状态与事务引擎，注入纯规则和内容定义。
4. 建立 M04 视图与资源呈现及 M06 布局能力，再建立依赖正式状态和已有线形能力的 M08 关系服务。
5. 建立 M03 图片协调和各模块工具控制器，M07 的选择工具此时接入已有视图；随后建立 M09 保存调度与导出器。
6. 建立 M01 管理服务，再建立依赖它的 M05 搜索定位；UI 工厂此时装配依赖，不能让管理服务构造反过来等待搜索页面。
7. M10 外壳接收页面和工具贡献，挂载后显式启动订阅；关闭时按相反顺序结束工具、保存和释放资源。

服务构造不启动其他服务。M09 资源仓先于画布，导出器在渲染器之后；M07 核心先于选择工具和界面。工厂通过参数取得实际服务，禁止用全局查找器隐藏初始化循环。

应用只装配和维护共享生命周期，M01 的切换、M05 的定位、M03 的收纳分别留在各自模块。

## 兼容与验证

协议变更记录版本、主责、受影响模块和迁移安排。模块实现只从两个共享包及自身公开入口引用，应用装配可引用全部模块。新增字段和类型同步更新 codec、引用重写、检索提取、绘制与验证。

实现后分别检查静态包依赖无环、类型注册、事务原子性、版本冲突、取消、索引追赶、保存回执、UI 工厂挂载和两端生命周期。当前文档检查不能替代 Dart 分析、Flutter 构建、真实接口与设备验收。
