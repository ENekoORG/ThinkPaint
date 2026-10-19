# ThinkPaint 统一技术路线候选

更新日期：2026-10-19。统一路线已采用 A：Flutter + Dart + 自定义绘制画布。本文件保留历史比较依据，当前决定见 [技术路线决定](architecture/PLATFORM_DECISION.md)。

推荐采用 A：Flutter、Dart 与自定义绘制画布。它能共用 Android 和 Windows 的主要界面、业务逻辑与绘制代码，适合以图片、文字、手写和关系线为核心的小团队项目。B：Qt 6、C++ 与 Qt Quick/QML，适合优先投入桌面数位板兼容和底层图形控制；C：C#、Avalonia 与 Skia 自绘，适合已有 C# 积累或更偏 Windows 编辑器的团队。

## 选择依据

ThinkPaint 的重点为双端可编辑线索板，包括压感手绘、鼠标箭头、大量图片、文字输入和无限视图。当前范围没有要求专业纹理笔刷或以倾斜着色为核心的绘画能力。

笔输入比较依据平台后端是否实际传递数据。统一接口具有压力或倾斜字段，仍需对应设备、驱动及平台后端支持。性能比较区分框架提供的绘制能力和应用需要完成的场景管理；开发成本评价属于工程判断。

源码核查基线为 Flutter 3.47.5、Avalonia 12.1.4，以及 Qt 6.12 分支对应的 `qtbase` 提交 `86f8b96940cf134ab8d746c027f0efb018520a9b`。官方文档查阅日期为 2026-10-19。

## A Flutter 与 Dart

### 手写笔支持

Android 后端区分笔、倒置笔与手指，读取压力、倾斜和笔按钮。Windows 后端使用 Windows Pointer 输入读取压力、旋转、倒置笔和按钮，并将压力传给引擎，已有支持压感的实际数据路径。[Android 后端源码](https://github.com/flutter/flutter/blob/3.47.5/engine/src/flutter/shell/platform/android/io/flutter/embedding/android/AndroidTouchProcessor.java#L372)、[Windows 笔事件](https://github.com/flutter/flutter/blob/3.47.5/engine/src/flutter/shell/platform/windows/flutter_window.cc#L567)、[Windows 压力转发](https://github.com/flutter/flutter/blob/3.47.5/engine/src/flutter/shell/platform/windows/flutter_windows_view.cc#L627)

Windows 的上述路径没有传递倾斜，嵌入器事件结构也缺少倾斜字段。传统仅提供 WinTab 的数位板、高级笔属性和高频输入需要另行检查。当前还有 Windows 触摸产生异常鼠标按下状态的官方问题报告，应纳入笔、鼠标和触摸混用验证。[嵌入器事件结构](https://github.com/flutter/flutter/blob/3.47.5/engine/src/flutter/shell/platform/embedder/embedder.h#L1280)、[Windows 触摸问题 192991](https://github.com/flutter/flutter/issues/192991)

### 无限画布性能

Flutter 提供自定义绘制与加速渲染。`CustomPainter` 可以按重绘通知更新内容而跳过界面构建和布局，适合连续绘画和视图移动；当前官方文档说明 Android 和 Windows 均有 Impeller 渲染支持。[CustomPainter](https://api.flutter.dev/flutter/rendering/CustomPainter-class.html)、[Impeller](https://docs.flutter.dev/perf/impeller)

ThinkPaint 仍需实现可见范围筛选、笔迹缓存、按缩放调整图片清晰度及资源预算。以统一绘制程序呈现内容，避免大量细小界面元素参与完整布局。缓存与图层同样消耗内存，需要按实际负载测量。

### 图片处理

内建解码覆盖 PNG、JPEG 和 WebP 等格式，可指定输出尺寸，画布可完成显示裁剪、旋转、缩放和透明度。`instantiateImageCodec` 的尺寸参数描述为解码后缩放，不能据此保证超大原图首次解码的峰值内存下降。底层图片对象也需要明确释放。[图片解码](https://api.flutter.dev/flutter/dart-ui/instantiateImageCodec.html)、[图片资源释放](https://api.flutter.dev/flutter/dart-ui/Image/dispose.html)

原图保存、缩略图生成、多实例共享、可见范围加载和缓存淘汰仍属于应用工作。当前图片功能具备实现基础，复杂滤镜不属于选型的必要条件。

### 开发和维护成本

预计三套中整体迭代成本最低。主要代码使用 Dart，界面和绘制可以共用，适合十个模块采用统一语言协作。系统文件入口、特殊笔输入和平台问题仍可能涉及 Windows C++ 或 Android Kotlin/Java 的小范围扩展。

Flutter 核心为 BSD 三条款许可，具体依赖单独检查。[Flutter 3.47.5 许可证](https://github.com/flutter/flutter/blob/3.47.5/LICENSE)

## B Qt 6 与 C++ 及 Qt Quick

QML 是描述界面的语言；C++ 负责计算、对象处理与需要细致控制的图形工作。Qt 官方支持 Android 和 Windows。[支持平台](https://doc.qt.io/qt-6/supported-platforms.html)

### 手写笔支持

Windows 后端同时具备 Pointer 笔输入和 WinTab 路径，源码覆盖压力、倾斜、旋转、侧键与橡皮等信息。它对传统桌面数位板兼容具有较好的基础，但实际支持范围仍取决于设备和驱动模式。[Windows Pointer 源码](https://github.com/qt/qtbase/blob/86f8b96940cf134ab8d746c027f0efb018520a9b/src/plugins/platforms/windows/qwindowspointerhandler.cpp)、[WinTab 后端](https://github.com/qt/qtbase/blob/86f8b96940cf134ab8d746c027f0efb018520a9b/src/plugins/platforms/windows/qwindowstabletsupport.cpp)

Android 桥接传递压力、按钮与笔类别，但核查版本把倾斜和旋转参数传为零。若这些高级属性成为必要能力，仍需 Android 适配。[Android 桥接源码](https://github.com/qt/qtbase/blob/86f8b96940cf134ab8d746c027f0efb018520a9b/src/plugins/platforms/android/androidjniinput.cpp#L381)

### 无限画布性能

Qt Quick 场景图保留图形数据并交给图形系统处理，支持自定义几何、纹理与批量绘制，提供较细的性能控制。实现高负载画板需要相应的图形和资源生命周期管理能力。[Qt Quick 场景图](https://doc.qt.io/qt-6/qtquick-visualcanvas-scenegraph.html)

核心画板应评估场景图自绘。QML `Canvas` 使用图片表面绘制，官方提醒大面积、频繁更新可能带来纹理上传成本；默认场景图也没有自动完成应用层的可见范围筛选。[Canvas 限制](https://doc.qt.io/qt-6/qml-qtquick-canvas.html)、[默认渲染器行为](https://doc.qt.io/qt-6/qtquick-visualcanvas-scenegraph-renderer.html)

Qt 的优势是控制能力，实际流畅度需要与其他路线使用相同画板、设备和操作验证。

### 图片处理

`Image` 提供异步加载、目标加载尺寸与缓存；`QImageReader` 提供缩放、裁剪和方向信息处理。解码阶段能减少多少内存取决于格式插件，仍需自己管理原图、缩略图和纹理预算。[Image](https://doc.qt.io/qt-6/qml-qtquick-image.html)、[QImageReader](https://doc.qt.io/qt-6/qimagereader.html)

### 开发和维护成本

预计整体成本最高。需要同时维护 C++、QML、渲染线程、图形资源，以及 Android 原生构建与打包。性能问题可以深入处理，但开发、调试和贡献者协作需要更多专业知识。

Qt 各模块许可不完全相同，存在 LGPL、GPL 和商业许可组合，依赖选定时需逐模块核对。[Qt 许可说明](https://doc.qt.io/qt-6/licensing.html)

## C C# 与 Avalonia 及 Skia

Avalonia 提供跨平台界面，Skia 是底层二维图形库。采用 C# 编写主要逻辑，通过自定义绘制构建画板。

### 手写笔支持

Avalonia 12.1.4 的 Android 后端已经区分笔和橡皮，传递压力、历史点和笔按钮；核查路径没有读取倾斜轴。Windows 后端读取压力、旋转、X/Y 倾斜和历史点，并处理笔按钮与橡皮。[Android 笔事件源码](https://github.com/AvaloniaUI/Avalonia/blob/12.1.4/src/Android/Avalonia.Android/Platform/Specific/Helpers/AndroidMotionEventsHelper.cs)、[Windows 笔事件源码](https://github.com/AvaloniaUI/Avalonia/blob/12.1.4/src/Windows/Avalonia.Win32/WindowImpl.AppWndProc.cs)

传统 WinTab-only 驱动和厂商笔功能仍需专门验证。其官方平台分级当前把 Android 16 列为 Tier 1，Android 12 至 15 列为 Tier 2；Tier 2 采用尽力支持，需要自行覆盖目标平板的验证。[平台支持分级](https://docs.avaloniaui.net/docs/supported-platforms)

### 无限画布性能

Avalonia 使用 Skia，提供自定义绘制与渲染线程扩展，能够集中处理笔迹和图片。使用这些能力仍需处理可见范围、缓存和跨线程数据，不能只依靠普通界面控件获得大场景性能。[自定义绘制](https://docs.avaloniaui.net/docs/graphics-animation/custom-rendering)

### 图片处理

`Bitmap` 提供读取、目标宽高解码和保存等能力，自绘可完成裁剪、旋转及叠加，图片资源需要明确释放。GPU 自绘内容与普通控件截图的行为不同，导出应单独验证，并保持与画板相同的内容语义。[Bitmap API](https://docs.avaloniaui.net/api/avalonia/media/imaging/bitmap)、[绘制与离屏输出](https://docs.avaloniaui.net/docs/graphics-animation/custom-rendering)

### 开发和维护成本

预计居中。C# 与 .NET 对对象模型、文件处理和桌面工具界面较友好，已有 C# 经验时成本可明显下降。Android 的构建工具链、生命周期和设备支持仍需要持续维护。没有既有 C# 积累时，本项目缺少足够理由优先于 Flutter。

Avalonia 核心采用 MIT 许可，附加产品与具体依赖单独核对。[Avalonia 许可证](https://github.com/AvaloniaUI/Avalonia/blob/12.1.4/licence.md)

## 共同的图片和性能约束

图片压缩文件大小不代表解码后内存。以 6000 × 4000 像素、每像素四字节估算，单份像素数据约为 96 MB，尚未计入其他副本、纹理和缓存。该数值为容量计算，不能作为某套框架的实测占用。

三套方案均需控制可见内容、图片清晰度和资源生命周期。选择 Qt 提供更多底层控制，选择 Flutter 或 Avalonia 可以减少部分界面开发工作；最终支持多少图片和笔迹，由相同负载下的设备测试决定。

## 推荐和改选条件

推荐 A，优先保证双端功能可持续迭代、统一绘制和模块协作。Flutter 当前 Windows 压力链路已有源码证据，适合作为首选候选；Windows 目标数位板的实际兼容列为首项验证。

- 如果目标数位板在正常驱动模式下，Flutter 无法稳定获得必要的压力、橡皮或按键，而 Qt 能稳定提供，并且这些设备属于首版必须支持范围，改选 B。
- 如果产品转向专业绘画，广泛传统数位板兼容、复杂笔刷和更细图形控制成为主要目标，优先重新评估 B。
- 如果核心维护成员已有较强 C#/.NET 经验或需要复用现有 C# 代码，并接受 Android 的支持与验证范围，可优先考虑 C。

## 选定后的最小验证

在规划实际接口与工程前，先明确目标设备和必要能力。得到实施授权后，用真实设备检查压力变化、侧键和橡皮、笔触摸共存、失焦取消，以及中文输入。

画布试验使用相同图片和笔迹，逐级增加内容，测量连续平移缩放的帧耗时、输入延迟和内存峰值。再检查大图反复缩放、资源释放和 Android 后台恢复。目标在运行前约定，失败条件与改选条件一起记录。

实际共享接口与工程结构见 [共享接口](architecture/SHARED_CONTRACTS.md)和[工程结构](architecture/PROJECT_STRUCTURE.md)。
