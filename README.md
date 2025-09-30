# Swift 编程语言中文翻译

这是 Swift 官方文档（The Swift Programming Language）的中文翻译与整理项目，使用 VitePress 构建静态站点，目标是提供高质量、及时同步的中文文档，方便中文读者学习与查阅。

> 友情提示：本仓库仅用于技术学习与交流，不代表 Apple 官方立场。欢迎参与翻译与校对。

---

## 快速开始

前置要求：

- 推荐 Node.js 18+（VitePress 2 需要较新的 Node 版本）
- 包管理器建议使用 pnpm（仓库已包含 `pnpm-lock.yaml`）

安装依赖与本地开发：

```bash
pnpm install
pnpm run docs:dev
```

启动后访问本地开发地址（终端会显示，通常为 `http://localhost:5173`）。

构建与本地预览构建产物：

```bash
pnpm run docs:build
pnpm run docs:preview
```

可用脚本（来自 `package.json`）：

- `docs:dev`: 启动本地开发服务器
- `docs:build`: 构建静态站点
- `docs:preview`: 预览构建产物

---

## 目录结构

仓库核心文档位于 `docs/` 目录下，主要结构如下：

- `docs/index.md`: 文档首页
- `docs/a-swift-tour.md`: Swift 一日游（导览）
- `docs/guide/`: 语言指南各章节（基础、集合、闭包、并发、泛型等）
- `docs/reference/`: 语言参考（词法、类型、声明、表达式、语句等）
- `docs/version-compatibility.md`: 版本兼容性说明与历史

其他仓库根目录文件：

- `about.md`: 关于项目与贡献者（可补充）
- `index.md`: 站点入口（可能用于根级路由）

---

## 贡献指南

非常欢迎参与翻译与校对，任何规模的改进都有价值：

1. Fork 本仓库并创建分支（建议命名如 `feat/chapter-xx` 或 `fix/typo-xxx`）。
2. 进行翻译或修订，确保本地 `pnpm run docs:dev` 预览无报错、导航与链接可达。
3. 提交 Pull Request，PR 描述中请简述：改动范围、主要术语选择、是否需要他人协作/校对。
4. 等待 Review 与合并；审阅意见可在 PR 中讨论并跟进修改。

建议提交信息格式：

```text
docs(guide/closures): 初译“闭包”一章的捕获列表与逃逸闭包小节
```

---

## 翻译与风格约定

- 忠实原文、通顺易读、术语统一。
- 文件命名：保持英文、短横线风格（如 `optional-chaining.md`），路径与上游结构一致。
- 链接：使用相对路径链接站内文档，避免绝对域名。
- 代码块：Swift 代码使用语言标注 `swift`，终端命令使用 `bash`。
- 标点与排版：中英文之间留空格，数字与单位之间留空格（例如：`5 ms`）。
- 专有名词：保留 Swift 专有名词的大小写（如 Protocol、Optional、Actor）。
- 术语表：若遇争议术语，请在 PR 说明中列出选择与理由，便于统一。

---

## 校对流程

- 初稿 PR 可标注为 Draft，方便边讨论边完善。
- 需要二次校对的章节可在 PR 描述中 @ 相关维护者或贡献者。
- 合并前至少通过一次本地预览检查导航、交叉引用与代码高亮。

---

## 常见问题（FAQ）

- 本地无法启动或白屏：请确认 Node.js 版本为 18+，并删除缓存后重装依赖：
  
  ```bash
  rm -rf node_modules pnpm-lock.yaml
  pnpm install
  pnpm run docs:dev
  ```
  
- 中文显示异常：请确保终端与编辑器均为 UTF-8 编码；VitePress 默认支持中文无需额外配置。
- 版本差异：若原文有版本差异或新增章节，请参考 `docs/version-compatibility.md` 并在 PR 中说明对应 Swift 版本。

---

## 致谢

- 原文与版权归 Apple 所有：The Swift Programming Language（swift.org）。
- 感谢所有贡献翻译、校对与问题反馈的社区成员。

如果本项目对你有帮助，欢迎 Star、转发或提交改进建议！
