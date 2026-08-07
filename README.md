# SQLite 文档中文翻译

本项目是 [SQLite 官方文档](https://sqlite.org/) 的中文翻译仓库。

**翻译说明：** SQLite 官方文档目前已进入**公共领域（Public Domain）**，允许自由复制、分发和再创作。

## 项目背景

此前 [Water-Run/llm-translate-documents](https://github.com/Water-Run/llm-translate-documents) 曾用大语言模型翻译过 SQLite 文档，但由于上下文限制，对 `capi3ref` 等大型文档在刚开始就中断了，目前还没有完整的翻译文件，所以做了本项目，对 SQLite 文档进行完整翻译。

## 现状与计划

- 当前已翻译：*C-language Interface Specification for SQLite*（[capi3ref](https://sqlite.org/capi3ref.html)，SQLite 版本 3 的 C 语言接口规范）一页，见 `capi3ref_完整翻译.md`。
- **日后将陆续在本仓库翻译更多 SQLite 官方文档**，逐步覆盖全套文档。

## 文件说明

- `capi3ref_完整翻译.md`：capi3ref 一页的中文翻译（单文件，含名词库 / 翻译对照表与正文）

## 翻译约定

- 本翻译由 **DeepSeek Flash 0731** 完成，未经人工逐句校对。
- **文本内容仅供参考**，如有出入，**实际以官方文档为准**。
- 对象、常量、函数名（`sqlite3_*`、`SQLITE_*` 等）一律保留英文；说明文字译为中文。

原文地址：<https://sqlite.org/capi3ref.html>
