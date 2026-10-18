# ThinkPaint

ThinkPaint 是面向 Android 和 Windows 的开源线索板项目，在可向四个方向持续扩展的画板上摆放图片、文字、手绘和关系线，并通过档案盒与主题区域整理资料。

## 功能分类

1. [管理](docs/modules/M01-management.md)：应用主页和多画板管理。
2. [绘图](docs/modules/M02-drawing.md)：笔、数位板、鼠标绘制与自由箭头。
3. [图片](docs/modules/M03-images.md)：图片导入、档案盒和图片批注。
4. [画布](docs/modules/M04-canvas.md)：扩展、坐标、查看和内容适配。
5. [搜索](docs/modules/M05-search.md)：检索、筛选和结果定位。
6. [文字与便签](docs/modules/M06-text.md)：中文输入、文本样式和布局。
7. [对象编辑与排版](docs/modules/M07-editing.md)：选择、组合、区域、排版和撤销事务。
8. [线索与关系](docs/modules/M08-relations.md)：绑定连线、资料标签和关系专注。
9. [保存与导出](docs/modules/M09-storage.md)：项目文件、资源、恢复、双端传递和导出。
10. [界面与设备适配](docs/modules/M10-platform.md)：外壳、输入、设置和外观。

## 项目文档

- [需求总览](docs/REQUIREMENTS.md)：功能分类、公共行为和原 17 项需求追溯。
- [模块开发计划](docs/DEVELOPMENT_PLAN.md)：每类独立开发、分支和联合验收安排。
- [模块协作约定](docs/MODULE_CONTRACTS.md)：接口责任和跨模块数据操作。
- [任务交接](docs/TASK_HANDOFF.md)：模块任务卡和发给开发对话的说明。
- [当前进度](PROGRESS.md)与[工作约定](AGENTS.md)。

## 开发状态

当前处于需求分类与模块规划阶段。各类别使用独立负责对话、模块分支和工作目录，通过公共接口增量集成。主技术路线与真实代码目录在获授权的工程装配工作中确定。

远程仓库：[ENekoORG/ThinkPaint](https://github.com/ENekoORG/ThinkPaint)。
